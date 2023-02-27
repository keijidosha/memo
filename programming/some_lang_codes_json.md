# コード比較: JSON

## デコード

### 例1

次のように配列、map を含む JSON ファイルを読み込む
```json
{
    "name": "hoge",
    "id": 1,
    "enable": true,
    "vehicles": {
        "bycicle": {
            "parts": [ "tire", "handle", "spoke" ],
            "topspeed": 52.375
        },
        "car": {
            "topspeed": 256.295,
            "parts": [ "tire", "handle", "wheel" ]
        }
    }
}
```

PHP と Ruby は解析した JSON オブジェクトから値を取り出していく。  
Kotlin と Go は用意しておいたクラスにセットする。

#### PHP
```php
<?php
    $root = json_decode( file_get_contents( "hoge.json" ), true );
    printf( "name=%s, id=%d, enable=%s\n", $root["name"], $root["id"], $root["enable"] );
    foreach( $root["vehicles"] as $k => $v ) {
        echo $k, "\n";
        foreach( $v["parts"] as $p ) {
            echo $p, ", ";
        }
        echo "\n" . $v["topspeed"] . "\n";
    }
?>
```

#### Ruby
```ruby
require 'json'

root = JSON.parse( File.open( "hoge.json" ).read )
printf "name=%s, id=%d, enable=%s\n", root["name"], root["id"], root["enable"]
root["vehicles"].each { |k, v|
    puts k
    v["parts"].each { |part|
        printf "%s, ", part
    }
    printf "\n%s\n", v["topspeed"]
}
```

#### Kotlin
```kotlin
import java.io.*
import java.math.*
import com.fasterxml.jackson.databind.*
import com.fasterxml.jackson.annotation.*

@JsonIgnoreProperties(ignoreUnknown=true)
class Hoge {
    lateinit var name: String
             var id: Int = 0
             var enable: Boolean = false
    @JsonIgnoreProperties(ignoreUnknown=true)
    class Vehicle {
        lateinit var parts: List<String>
        lateinit var topspeed: BigDecimal
    }
    lateinit var vehicles: Map<String, Vehicle>
}

fun main( args: Array<String> ) {
    val path = "hoge.json"

    val hoge = ObjectMapper().readValue( File( path ), Hoge::class.java )

    println( "name=${hoge.name}, id=${hoge.id}, enable=${hoge.enable}" )

    hoge.vehicles.forEach { k, v ->
        println( k )
        v.parts.forEach {
            print( "${it}, " )
        }
        println( "\n${v.topspeed}" )
    }

}
```

#### Go
```go
package main

import (
    "encoding/json"
    "io/ioutil"
    "fmt"
    "log"
)

type Vehicle struct {
    Parts       []string    `json:"parts"`
    TopSpeed    float64     `json:"topspeed"`
}

type Hoge struct {
    Name        string  `json:"name"`
    Id          int     `json:"id"`
    Enalbe      bool    `json:"enable"`
    Vehicles    map[string]Vehicle  `json:"vehicles"`
}

func main() {
    log.SetFlags(log.Llongfile)

    var path string
    path = "hoge.json"

    bytes, err := ioutil.ReadFile( path )
    if err != nil {
        log.Fatal( err )
    }

    var hoge Hoge
    if err := json.Unmarshal( bytes, &hoge ); err != nil {
        log.Fatal( err )
    }

    fmt.Printf( "name=%s, id=%d, enable=%v\n", hoge.Name, hoge.Id, hoge.Enalbe )
    for key, vehicle := range hoge.Vehicles {
        fmt.Println( key )
        for _, part := range vehicle.Parts {
            fmt.Print( part + ", " )
        }
        fmt.Printf( "\n%f\n", vehicle.TopSpeed )
    }

}
```

### 例2

次の sample.json を読み込む例
```json
{
    "status":"OK",
    "error":false,
    "no":1,
    "ratio":1.23,
    "list":[1,2,3],
    "detail":{
        "message":"Hello",
        "type":"string"
    },
    "people":{
        "Mike":{"age":16,"address":"New York"},
        "Merry":{"age":17,"address":"Los Angels"}
    }
}
```

#### Go

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
)

type detail struct {
    Message string `json:"message"`
    Type2   string `json:"type"`
}
type personalInfo struct {
    Age     int    `json:"age"`
    Address string `json:"address"`
}

type jsonDataType struct {
    Status  string                  `json:"status"`
    Err     bool                    `json:"error"`
    No      int                     `json:"no"`
    Ratio   float32                 `json:"ratio"`
    List    []int                   `json:"list"`
    Details detail                  `json:"detail"`
    People  map[string]personalInfo `json:"people"`
}

func main() {
    bytes, err := ioutil.ReadFile("sample.json")
    if err != nil {
        log.Fatal(err)
    }
    var jsonData jsonDataType
    if err := json.Unmarshal(bytes, &jsonData); err != nil {
        log.Fatal(err)
    }

    fmt.Printf("status: %s\n", jsonData.Status)
    fmt.Printf("error : %v\n", jsonData.Err)
    fmt.Printf("Number: %d\n", jsonData.No)
    fmt.Printf("Ratio : %f\n", jsonData.Ratio)
    fmt.Println("Details")
    for list := range jsonData.List {
        fmt.Printf("  Detail: %d\n", list)
    }
    fmt.Printf("Detail.Message: %s\n", jsonData.Details.Message)
    fmt.Printf("Detail.Type   : %s\n", jsonData.Details.Type2)
    for name, person := range jsonData.People {
        fmt.Println(name)
        fmt.Printf("  Age    : %d\n", person.Age)
        fmt.Printf("  Address: %s\n", person.Address)
    }
}
```

## エンコード

上記例1 の JSON を生成する

### Go
```golang
package main

import (
    "encoding/json"
    "fmt"
    "log"
)

type Vehicle struct {
    Parts    []string `json:"parts"`
    TopSpeed float64  `json:"topspeed"`
}

type Hoge struct {
    Name     string             `json:"name"`
    Id       int                `json:"id"`
    Enalbe   bool               `json:"enable"`
    Vehicles map[string]Vehicle `json:"vehicles"`
}

func main() {
    data := Hoge{}
    data.Name = "hoge"
    data.Id = 1
    data.Enalbe = true

    bicycle := Vehicle{}
    bicycle.Parts = []string{"tire", "handle", "spoke"}
    bicycle.TopSpeed = 52.375

    car := Vehicle{}
    car.Parts = []string{"tire", "handle", "wheel"}
    car.TopSpeed = 256.295

    data.Vehicles = make(map[string]Vehicle)
    data.Vehicles["bicycle"] = bicycle
    data.Vehicles["car"] = car

    json, err := json.Marshal(data)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(string(json))
}
```

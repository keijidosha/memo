# コード比較: http

Table of Contents
=================
* [GET](#get)
   * [PHP](#php)
   * [Ruby](#ruby)
   * [Java](#java)
   * [Kotlin](#kotlin)
   * [Go](#go)
* [POST](#post)
   * [PHP](#php-1)
   * [Ruby](#ruby-1)
   * [Java](#java-1)
   * [Kotlin](#kotlin-1)
   * [Go](#go-1)

## GET
### PHP
```php
<?php
    echo file_get_contents( "http://www.yahoo.co.jp/" );
?>
```
302 Moved に追従する

### Ruby
```ruby
require "net/http"
puts Net::HTTP.get( URI.parse( "http://www.yahoo.co.jp/" ))
```
302 Moved に追従しない

### Java
```java
import java.io.*;
import java.net.*;

public class Get {
    public Get() {
        try( Reader reader = new InputStreamReader(
                    new URL( "http://www.yahoo.co.jp/" ).openConnection().getInputStream())) {
          StringBuilder sb = new StringBuilder( 4096 );
          char[] buf = new char[1024];
          int len;
          while(( len = reader.read( buf )) >= 0 ) {
              sb.append( buf, 0, len );
          }
          System.out.println( sb );
        } catch( IOException ex ) {
            ex.printStackTrace();
        }
    }

    public static void main( String[] args ) {
        new Get();
    }
}
```
302 Moved に追従する

### Kotlin
```kotlin
import java.net.*

fun main( args: Array<String> ) {
    println( URL( "http://www.yahoo.co.jp/" ).readText())
}
```
302 Moved に追従する

### Go
```go
package main

import (
    "net/http"
    "io/ioutil"
    "fmt"
)

func main() {
    url := "http://www.yahoo.co.jp/"
    res, _ := http.Get( url )
    defer res.Body.Close()

    byteArray, _ := ioutil.ReadAll( res.Body )
    fmt.Println( string( byteArray ))
}
```
302 Moved に追従する

***

## POST

### PHP
```php
<?php
$data = array(
    "name" => "iPhone",
    "price" => "100000",
  );
$options = array(
    "http" => array(
      "method" => "POST",
      "content" => http_build_query( $data ),
    )
  );
echo file_get_contents( "http://localhost:8000/post_server.php", false, stream_context_create( $options ));
?>
```

### Ruby
```ruby
require "net/http"

puts Net::HTTP.post_form( URI.parse( "http://localhost:8000/post_server.php" ),
    { "name" => "iPhone",
      "price" => "100000" }).body
```

### Java
```java
import java.io.*;
import java.net.*;

public class Post {
    public Post() {
    try {
        URLConnection conn = new URL( "http://localhost:8000/post_server.php" ).openConnection();
        conn.setDoOutput( true );
        try( Writer writer = new OutputStreamWriter( conn.getOutputStream())) {
            writer.write( "name=iPhone&price=10000");
            writer.flush();
        }

        try( Reader reader = new InputStreamReader( conn.getInputStream())) {
            char[] buf = new char[4096];
            int len;
            StringBuilder sb = new StringBuilder();
            while(( len = reader.read( buf )) >= 0 ) {
                sb.append( buf, 0, len );
            }
            System.out.println( sb );
        }
    } catch( IOException ex ) {
        ex.printStackTrace();
    }
}

    public static void main( String[] args ) {
        new Post();
    }
}
```

### Kotlin
```kotlin
import java.io.*
import java.net.*

fun main( args: Array<String> ) {
    URL( "http://localhost:8000/post_server.php" ).openConnection().apply {
        doOutput = true
        outputStream.writer().use() {
            it.write( "name=iPhone&price=10000")
            it.flush()
        }
        inputStream.reader().use() {
            println( it.readText())
        }
    }
}
```
### Go
```go
package main

import (
    "net/http"
    "net/url"
    "io/ioutil"
    "fmt"
    "strings"
    "time"
)

func main() {
    urlStr := "http://localhost:8000/post_server.php"
    values := url.Values{}
    values.Add( "name", "iPhone")
    values.Add( "price", "10000")

    req, err := http.NewRequest( "POST", urlStr, strings.NewReader( values.Encode()))
    if err != nil {
        fmt.Println( err )
        return
    }

    req.Header.Add( "User-Agent", "Go" )
    req.Header.Add("Content-Type", "application/x-www-form-urlencoded")

    req.SetBasicAuth( "user", "password" )

    client := &http.Client{ Timeout: time.Duration(10 * time.Second) }
    res, err := client.Do( req )
    // 1行で書くと次のようになる
    // res, err := ( &http.Client{ Timeout: time.Duration( 10 * time.Second ) } ).Do( req )
    if err != nil {
        fmt.Println( err )
        return
    }

    defer res.Body.Close()

    byteArray, _ := ioutil.ReadAll( res.Body )
    fmt.Println( string( byteArray ))
}
```

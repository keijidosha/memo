# Go: 文字列

## 数値
* 数値変換
  * int32 に変換
    ```go
    import "strconv"
    import "fmt"
  
    func main() {
        num, err := strconv.Atoi("123")
        if err != nil {
            log.Fatal(err)
        }
        fmt.Println(num)
    }
    ```
  * int64 に変換  
    `num, err := strconv.ParseInt("1234567890123456789",10,64)`
  * uint64 に変換  
    `num, err := strconv.ParseUint("1234567890123456789",10,64)`
* 数値から文字列に変換  
  ```go
  fmt.Println(strconv.Itoa(123))
  ```

## 文字列操作

* 文字列が xxx で始まるかチェック  
  ```
  strings.HasPrefix(str, "xxx")
  ```
* substring  
  ```
  str[3:6]  // 0 origin で 3から5バイトまでの 3文字
  str[3:]   // 0 origin で 3バイト目から末尾まで
  str[:6]
  ```
* 分割  
  `strings.Split("p1=111,p2=222,p3=333", ",")`
* 結合  
  ```
  stringList := []string{"hoge", "fuga")
  str := strings.Join(stringList, ",")
  ```

## 文字列置換

  ```go
  str := "Hello Mike !"
  replacedStr := strings.Replace(str, "Hello", "Hi", 1)
  fmt.Println( replacedStr )
  ```  
  第1パラメータ: 置換対象文字列  
  第2パラメータ: 置換前文字列  
  第3パラメータ: 置換後文字列  
  第4パラメータ: 置換回数(-1: すべて置換)

## 正規表現

  ```go
  package main

  import (
      "fmt"
      "regexp"
      "strings"
  )

  var rgx = regexp.MustCompile(`(\d{2}):(\d{2}):(\d{2})`)

  func main() {
      if result := rgx.FindStringSubmatch("09:15:30"); len(result) >= 4 {
          fmt.Println("Hour=", result[1])
          fmt.Println("Minute=", result[2])
          fmt.Println("Second=", result[3])
      }
  }
  ```
### 正規表現で文字列置換

```
package main

import (
    "fmt"
    "regexp"
)

func main() {
    input := `There is 3 pens`
    fmt.Println(input)
    re := regexp.MustCompile(`^(There is )(\d+)`)
    idx := 10
    // $110 となると、110番目にマッチした要素を探すので、${1}110 となるようにする。
    rep := fmt.Sprintf("${1}%d", idx)
    result := re.ReplaceAllString(input, rep)
    fmt.Println(result)    // There is 10 pens
}
```

## その他

* トリム  
  ```go
  trimedStr = strings.Trim(" hoge ", " ")
  ```  
  第2パラメータ: 取り除く文字


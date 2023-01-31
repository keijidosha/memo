# Go: 日付・時刻

## 時刻取得
* UTC での現在時刻を取得する  
  ```Go
  utc, _ := time.LoadLocation("UTC")
  now := time.Now().In(utc)
  ```
## 文字列に変換
日付をフォーマットして文字列に変換

```go
now := time.Now()
fmt.Println(now.Format(time.RFC1123))
// Mon, 25 Nov 2019 11:55:35 JST
fmt.Println(now.Format(time.RFC1123Z))
// Mon, 25 Nov 2019 11:55:35 +0900
fmt.Println(now.Format(time.RFC822))
// 25 Nov 19 11:55 JST
fmt.Println(now.Format(time.RFC822Z))
// 25 Nov 19 11:55 +0900
fmt.Println(now.Format(time.RFC850))
// Monday, 25-Nov-19 11:55:35 JST
fmt.Println(now.Format(time.RFC3339))
// 2019-11-25T11:55:35+09:00
fmt.Println(now.Format(time.RFC3339Nano))
// 2019-11-25T11:55:35.587984+09:00

// ISO8601
fmt.Println(now.Format("2006-01-02T15:04:05.000Z"))
// 2019-11-25T11:55:35.587Z
fmt.Println(now.Format("2006-01-02T15:04:05.999Z"))
// 2019-11-25T11:55:35.587Z
```

## 文字列から変換

* "時:分:秒.マイクロ秒" を日時に変換  
   ```
   import "time"
   parsedTime, err := time.Parse("15:04:05.00000", "09:30:25.123456")
   ```
* スリープする時など、文字列から time.Duration に変換  
  ```
  sleepTime, err := strconv.Atoi("1000")
  if err != nil {
    log.Fatal(err)
  }
  time.Sleep(time.Duration(sleepTime) * time.Millisecond)
  ```


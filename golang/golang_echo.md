- Table of Content  
{:toc}

# echo

## 新規 echo プロジェクト作成

```bash
mkdir myapp && cd myapp
go mod init myapp
go get github.com/labstack/echo/v4
go mod tidy
```

## パラメーター取得

* パスパラメーター  
(例) /users/:id  
`id := c.Param("id")`
* クエリーパラメーター  
(例) /users?id=1  
`id := c.QueryParam("id")`
* POSTボディ(URL エンコード)  
(例) curl -X POST http://127.0.0.1/save -H 'Content-Type: application/x-www-form-urlencoded' –d ‘id=1’
`id := c.FormValue("id")`
* POST JSON ボディ  
(例) curl -X POST http://127.0.0.1/save -H 'Content-Type: application/json' –d ‘{"id":1,"name":"hoge"}’
  ```go
  type User struct {
    Id   int32  `json:"id"`
    Name string `json:"name"`
  }
  func save(ec echo.Context) error {
    u := new(User)
    if err := ec.Bind(u); err != nil {
      return ec.String(http.StatusBadRequest, "bad request")
    }
  }
  ```

## Keep-Alive タイムアウトを指定するサンプル

Keep-Aliveタイムアウトを 30秒に設定

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"time"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

const keepAliveTimeout = 30

func root(ec echo.Context) error {
	byteArray, _ := ioutil.ReadAll(ec.Request().Body)
	println(string(byteArray))

	ec.Response().Header().Add("Keep-Alive", fmt.Sprintf("timeout=%d", keepAliveTimeout))
	ec.Response().Header().Add("Connection", "keep-alive")

	return ec.String(http.StatusOK, "OK")
}

func main() {

	ec := echo.New()
	ec.Use(middleware.Logger())
	ec.Use(middleware.Recover())

	ec.POST("/", root)

	svr := &http.Server{
		Addr:        ":8080",
		IdleTimeout: time.Duration(keepAliveTimeout) * time.Second,
		Handler:     ec,
	}

	ec.Logger.Fatal(svr.ListenAndServe())
}
```

※デフォルトは無制限

## WebSocket

```
package main

import (
    "flag"
    "fmt"

    "github.com/gorilla/websocket"
    "github.com/labstack/echo/v4"
    "github.com/labstack/echo/v4/middleware"
    "github.com/natefinch/lumberjack"
    "github.com/rs/zerolog"
    "github.com/rs/zerolog/log"
)

var (
    upgrader = websocket.Upgrader{}
    useTls  bool
)

func websocketHandler(ec echo.Context) error {

    ws, err := upgrader.Upgrade(ec.Response(), ec.Request(), nil)
    if err != nil {
        return err
    }

    defer func() {
        // WebSocket プロトコル上のクローズ通知を送る
        _ = ws.WriteMessage(websocket.CloseMessage,
            websocket.FormatCloseMessage(websocket.CloseNormalClosure, "bye"))

        // 少し待つ（クローズ通知を受信側が読む猶予）
        time.Sleep(300 * time.Millisecond)

        // TCP 接続を閉じる
        _ = ws.Close()
    }()

    for {
        messageType, receivedData, err := ws.ReadMessage()
        if err != nil {
            // エラー処理
            if websocket.IsUnexpectedCloseError(
                // 予期せぬエラー
                err,
                websocket.CloseGoingAway,
                websocket.CloseAbnormalClosure) {
                log.Info().Msgf("%s", err)
            } else {
                log.Error().Msgf("%s", err)
            }
            return nil
        }

        switch messageType {
        case websocket.TextMessage:
            // テキストメッセージ
            message := string(receivedData)
            log.Info().Msgf("Received Message: %s", message)
        case websocket.BinaryMessage:
            // バイナリメッセージ
            log.Info().Msgf("Received %d bytes Binary Data", len(receivedData))
            if(len(receivedData) == 0) {
                break
            }
        default:
            log.Warn().Msgf("Unexpected Message Type: %s", messageId)
        }

        // テキストメッセージ送信
        ws.WriteMessage(websocket.TextMessage, []byte("OK"))
    }

    // 
    return nil
}

func main() {
    paramUseTls := flag.Bool("tls", false, "use tls")
    flag.Parse()
    useTls = *paramUseTls

    writer := &lumberjack.Logger{
        Filename:   "./BelugaBoxMock.log",
        MaxSize:    10,
        MaxBackups: 10,
        MaxAge:     30,
        Compress:   true,
        LocalTime:  true,
    }
    var logLevel zerolog.Level
    if isDebug {
        logLevel = zerolog.DebugLevel
    } else {
        logLevel = zerolog.InfoLevel
    }
    zerolog.SetGlobalLevel(logLevel)
    log.Logger = zerolog.New(writer).With().Timestamp().Logger()

    ec := echo.New()
    ec.Use(middleware.Recover())

    ec.Use(middleware.LoggerWithConfig(middleware.LoggerConfig{
        Output: writer,
    }))

    ec.GET("/ws/", websocketHandler)

    if useTls {
        // TLS を有効にする場合は証明書ふぁいる cert.pem と秘密鍵ファイル key.pem を用意しておく
        ec.Logger.Fatal(ec.StartTLS(fmt.Sprintf(":%d", listenPort), "cert.pem", "key.pem"))
    } else {
        ec.Logger.Fatal(ec.Start(fmt.Sprintf(":%d", listenPort)))
    }
}
```
オレオレ証明書を作成するコマンド例
```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 3650 -subj '/CN=*/' -nodes
```


## echo で zerolog を使う

go.mod
```go
require (
    github.com/labstack/echo/v4 v4.12.0
    gopkg.in/natefinch/lumberjack.v2 v2.2.1
)
```

```go
import (
	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
	"github.com/rs/zerolog"
	"github.com/rs/zerolog/log"
	"gopkg.in/natefinch/lumberjack.v2"
)


func main() {
	writer := &lumberjack.Logger{
		Filename:   "logs/zerolog.log",
		MaxSize:    1,
		MaxBackups: 3,
		MaxAge:     5,
		Compress:   true,
		LocalTime:  true,
	}
	zerolog.SetGlobalLevel(zerolog.DebugLevel)
	// タイムスタンプをミリ秒単位に設定(デフォルトは秒単位)
	zerolog.TimeFieldFormat = "2006-01-02T15:04:05.000Z"
	log.Logger = zerolog.New(writer).With().Timestamp().Logger()

	ec := echo.New()

        ec.Use(middleware.LoggerWithConfig(middleware.LoggerConfig{
            Output: writer,
        }))

	ec.Use(middleware.Logger())
	ec.Use(middleware.Recover())
}
```

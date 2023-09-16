## echo

### Keep-Alive タイムアウトを指定するサンプル

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

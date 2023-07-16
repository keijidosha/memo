## echo

### Keep-Alive タイムアウトを指定するサンプル

Keep-Aliveタイムアウトを 5秒に設定

```go
package main

import (
	"io/ioutil"
	"net/http"
	"time"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

func root(ec echo.Context) error {
	byteArray, _ := ioutil.ReadAll(ec.Request().Body)
	println(string(byteArray))

	return ec.String(http.StatusOK, "OK")
}

func main() {

	ec := echo.New()
	ec.Use(middleware.Logger())
	ec.Use(middleware.Recover())

	ec.POST("/", root)

	svr := &http.Server{
		Addr:        ":8080",
		IdleTimeout: time.Duration(5) * time.Second,
		Handler:     ec,
	}

	ec.Logger.Fatal(svr.ListenAndServe())
}
```

※デフォルトは無制限

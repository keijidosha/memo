## echo

### パラメーター取得

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
  ```
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

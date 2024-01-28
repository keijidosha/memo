# Go Command Line Parameter

- Table of Content  
{:toc}

## flag を使ったコマンドラインパラメーター取得

```
package main

import (
	"flag"
	"fmt"
)

func main() {
  // ***** パラメーター定義 *****
  // 整数パラメーター
	countParam := flag.Int("count", 1, "count of hoge")
  // 文字列パラメーター
	nameParam := flag.String("name", "Taro", "Input your name")
  // 真偽値パラメーター
	dryrunParam := flag.Bool("dryrun", false, "dry run")

  // パラメーター解析
  flag.Parse()

  fmt.Printf("count=%d\n",   *countParam)
  fmt.Printf("name=%s\n",    *nameParam)
  fmt.Printf("dry run=%v\n", *dryrunParam)
}
```

* flag のパラメーターに次の 3を指定
  * パラメーター名: コマンドラインに指定するパラメーター  
    (例) "count" を指定した場合、`-count=1` や `-count 1` といった感じに指定。
  * デフォルト値
  * 説明: --help や -h を指定した場合に表示される説明。



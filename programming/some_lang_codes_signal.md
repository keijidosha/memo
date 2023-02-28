# コード比較: シグナル

## Go
signal.Notify() を使って、channel 経由でシグナルを受信する

```golang
package main

import (
    "log"
    "os"
    "os/signal"
    "syscall"
)

func signalHandler(c chan os.Signal) {
OUT_OF_FOR:

    for sig := range c {
        log.Printf("%v", sig)
        switch sig {
        case syscall.SIGTERM:
            log.Println("program will be exit")
            break OUT_OF_FOR
        }
    }
}

func main() {
    c := make(chan os.Signal, 1)
    signal.Notify(c, syscall.SIGKILL, syscall.SIGHUP, syscall.SIGTERM, syscall.SIGINT, syscall.SIGQUIT)
    signalHandler(c)
}
```

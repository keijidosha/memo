- Table of Content  
{:toc}

# Go でのネットワークプログラミング

## ソケット通信

* サーバー側サンプル
  ```go
  package main
  
  import (
      "bufio"
      "fmt"
      "log"
      "net"
      "os"
      "time"
  )
  
  const listenPort = ":5555"
  
  func handleClient(conn net.Conn) {
      defer conn.Close()
  
      writer := bufio.NewWriter(conn)
  
      buf := make([]byte, 4096)
  
      // read timeout 10秒
      conn.SetReadDeadline(time.Now().Add(5 * time.Second))
  
      // read initial messages
      readedLen, err := conn.Read(buf)
      if err != nil {
          log.Printf("read error %v", err)
          return
      }
  
      message := string(buf[:readedLen])
      log.Print("received message: ", message)
  
      // 受信したメッセージをエコーバック
      // (1) WriteDeadline で送出する方法
      conn.SetWriteDeadline(time.Now().Add(3 * time.Second))
      conn.Write([]byte(message))
      // (2) Flush して送出する方法
      writer.WriteString(message)
      writer.Flush()
  }
  
  func main() {
      log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
  
      protocol := "tcp"
  
      tcpAddr, err := net.ResolveTCPAddr(protocol, port)
      if err != nil {
          log.Fatalf("ResolveTCPAddr error. %v", err)
      }
  
      listener, err := net.ListenTCP(protocol, tcpAddr)
      if err != nil {
          log.Fatalf("ListenTCP error. %v\n", err)
      }
  
      for {
          conn, err := listener.Accept()
          if err != nil {
              continue
          } else {
              log.Println("connected by ", conn.RemoteAddr().String())
          }
  
          go handleClient(conn)
      }
  }
  ```

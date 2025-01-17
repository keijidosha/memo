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
      listenPort := ":9000"
  
      tcpAddr, err := net.ResolveTCPAddr(protocol, listenPort)
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
* TLS 接続する場合のサンプル
  * 秘密鍵のパスフレーズあり
    ```go
    func loadEncryptedPrivateKey(keyPath, password string) (*rsa.PrivateKey, error) {
        // 秘密鍵ファイルを読み込む
        keyBytes, err := os.ReadFile(keyPath)
        if err != nil {
            return nil, fmt.Errorf("秘密鍵ファイルの読み込みに失敗: %v", err)
        }
    
        // PEMブロックのデコード
        keyBlock, _ := pem.Decode(keyBytes)
        if keyBlock == nil {
            return nil, fmt.Errorf("PEMのデコードに失敗")
        }
    
        // 暗号化された秘密鍵を復号
        decryptedKey, err := x509.DecryptPEMBlock(keyBlock, []byte(password))
        if err != nil {
            return nil, fmt.Errorf("秘密鍵の復号に失敗: %v", err)
        }
    
        // 秘密鍵のパース
        privateKey, err := x509.ParsePKCS1PrivateKey(decryptedKey)
        if err != nil {
            return nil, fmt.Errorf("秘密鍵のパースに失敗: %v", err)
        }
    
        return privateKey, nil
    }

    func main() {
        log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
    
        // 証明書の読み込み
        certBytes, err := os.ReadFile("server.pem")
        if err != nil {
            log.Fatalf("証明書の読み込みに失敗: %v", err)
        }
    
        // 暗号化された秘密鍵の読み込みと復号
        privateKey, err := loadEncryptedPrivateKey("server.key", "password")
        if err != nil {
            log.Fatalf("秘密鍵の読み込みに失敗: %v", err)
        }
    
        // 証明書と秘密鍵のペアを作成
        cert := tls.Certificate{
            Certificate: [][]byte{certBytes},
            PrivateKey:  privateKey,
        }
    
        tlsConf := &tls.Config{
            Certificates: []tls.Certificate{cert},
            MinVersion:   tls.VersionTLS12,
        }
    
        protocol := "tcp"
        listenPort := ":9000"
    
        listener, err := tls.Listen(protocol, listenPort, tlsConf)
        if err != nil {
            log.Fatalf("TLS listen error, %v", err)
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
  * 秘密鍵のパスフレーズなし
    ```go
    func main() {
        log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
    
        // サーバー秘密鍵がパスワードなしの場合の実装
        cert, err := tls.LoadX509KeyPair("server.pem", "server.key")
        if err != nil {
            fmt.Printf("load certificate error, %v", err)
            os.Exit(1)
        }
    
        tlsConf := &tls.Config{
            Certificates: []tls.Certificate{cert},
            MinVersion:   tls.VersionTLS12,
        }
    
        protocol := "tcp"
        listenPort := ":9000"
  
        listener, err := tls.Listen(protocol, listenPort, tlsConf)
        if err != nil {
            log.Fatalf("TLS listen error, %v", err)
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
    tls.LoadX509KeyPair() で証明書を取得。

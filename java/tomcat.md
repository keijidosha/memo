# Tomcat

* Windowsでサービス実行しているTomcatをリモートPCからjconsoleで監視できるようにする
  1. tomcat7w.exe を起動し、設定画面を開いて次の起動パラメータを追記
     ```
     -Dcom.sun.management.jmxremote
     -Dcom.sun.management.jmxremote.port=58080
     -Dcom.sun.management.jmxremote.ssl=false
     -Dcom.sun.management.jmxremote.authenticate=false
     -Djava.rmi.server.hostname=192.168.1.1
     ```
     「192.168.1.1」はこのTomcatが動いているPCの IPアドレスを指定  
     (複数の NIC がある場合は、接続元の PC が存在する方の NIC の IPアドレスを指定)
  1. Windowsファイアーウォールでポート 58080-65535 への接続を許可
* curl で Tomcat の WEBアプリをリロード  
`curl --basic --user <userid>:<password> http://localhost:8080/manager/text/reload?path=/<apppath>`  
または  
`curl http://<userid>:<password>@localhost:8080/manager/text/reload?path=/<apppath>`

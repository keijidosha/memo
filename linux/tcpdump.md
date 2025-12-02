# tcpdump

## 基本  
`tcpdump -i eth0 -s 1500 -w dump.pcap`  
対象ネットワークインターフェース: eth0  
キャプチャサイズ: 1500バイト(lo の場合は 65536)  
保存ファイル名: dump.pcap  

* tcp だけキャプチャする(または udp)  
`tcpdump -i eth0 -s 1500 -w dump.pcap tcp`
* DNSパケットだけをキャプチャする  
`tcpdump -i eth0 -s 1500 -w dump.pcap udp and port 53`
* 端末 192.168.1.10 とやり取りした HTTPパケットだけをキャプチャする  
`tcpdump -i eth0 -s 1500 -w dump.pcap tcp and port 80 and host 192.168.1.10`
* 端末 192.168.1.10 か 192.168.1.11 とやり取りした HTTPパケットだけをキャプチャする  
`tcpdump -i eth0 -s 1500 -w dump.pcap "tcp and port 80 and host (192.168.1.10 or 192.168.1.20)"`
* すべてのネットワークインターフェースを対象にする  
`-i any`

* キャプチャファイルをフィルターして特定のパケットだけを別ファイルに抽出
  ```
  tcpdump -r input.pcap "tcp and host 192.168.1.1 and port 8080" -w output.pcap
  ```
  Wireshark に付属の tshark を使う場合は
  ```
  tshark -r input.pcap -Y "ip.addr==192.168.1.1 && tcp.port=8080" -w output.pcap
  ```


## 応用

* tcpdump で 5分間キャプチャして終了する
  ```
  timeout 300 tcpdump -w dump.pcap tcp and port 80
  ```  
  次のように sudo の前に timeout を指定して tcpcump を実行すると、300秒経過しても tcpdump が終了しないので注意が必要(sudo -s などしてあらかじsめ root になって timeout を指定する)。  
  => 一般ユーザー権限で TERM シグナルを送信しても root 権限で動いている tcpdump を終了されられないためと思われる。
  ```
  timeout 300 sudo tcpdump -w dump.pcap tcp and port 80
  ```
  または次のように sudo + timeout を指定した場合は 300秒経過後に終了するが、途中で control + C で終了することはできない。途中でも終了できるようにしたい場合は、sudo -s などしてあらかじめ root になっておき、最初の例のように sudo なしで実行する。
  ```  
  sudo timeout 300 tcpdump -w dump.pcap tcp and port 80
  ```
  もう 1つの方法として、次のように 300秒でローテーションして、ローテーション数は 1回なのでそのまま終了する。
  ```
  tcpdump -w dump.pcap -W1 -G300 tcp and port 80
  ```  
  この場合、-G に指定した 300秒を経過後の最初のポート80 宛てのパケット受信後に終了する模様(終了するきっかけになった、300秒経過してからのパケットはキャプチャファイルには含まれない)。  
  指定時間経過してからポート 80 宛てのパケットが来ないと終了しない模様(ローテーションが必要、という判断を契機として終了する?)。

## リモートの tcpdump と、ローカルの WireShark を連携してキャプチャ・表示する
リモートホストに SSH 接続して tcpdump を実行  
キャプチャデータをローカルに転送して WireShark で受け取り表示

`sshpass -p <password> ssh <user>@<host> "tcpdump -U -n -w - -i eth0 'port 80'" | wireshark -k -i -`

※~~sshpass を使ってパスワード入力を省略すると、WireShark 終了時に tcpdump も終了する~~  
直接 ssh を実行してパスワード入力した場合は、WireShark 終了後もサーバ側に tcpdump プロセスが残るので、ログイン後 kill するなどして終了させる

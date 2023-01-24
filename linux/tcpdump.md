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

## リモートの tcpdump と、ローカルの WireShark を連携してキャプチャ・表示する
リモートホストに SSH 接続して tcpdump を実行  
キャプチャデータをローカルに転送して WireShark で受け取り表示

`sshpass -p <password> ssh <user>@<host> "tcpdump -U -n -w - -i eth0 'port 80'" | wireshark -k -i -`

※~~sshpass を使ってパスワード入力を省略すると、WireShark 終了時に tcpdump も終了する~~  
直接 ssh を実行してパスワード入力した場合は、WireShark 終了後もサーバ側に tcpdump プロセスが残るので、ログイン後 kill するなどして終了させる

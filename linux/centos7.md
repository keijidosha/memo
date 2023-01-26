# CentOS 7

Table of Contents
-----------------
* [CentOS 7 で変わったコマンド](#centos-7-で変わったコマンド)
  * [ネットワーク系](#ネットワーク系)
  * [firewall系](#firewall系)
  * [サービス系](#サービス系)
  * [サービスを作成](#サービスを作成)
  * [その他設定](#その他設定)

## CentOS 7 で変わったコマンド
### ネットワーク系
* IPアドレス表示(ifconfig -a)  
ip address show  
ip a s  
ip a  
ip a s enp1s0  
* IP アドレス設定  
ip a add 192.168.1.1/24 dev eth0
* ネットワークデバイスの MACアドレス一覧  
ip link  
ip l  
* ルーティングテーブル表示(netstat -rn)  
ip route  
ip r  
* デフォルトゲートウェイをルーティングテーブルに追加  
ip route add default via 192.168.1.254  
ip r add default via 192.168.1.254  
* デフォルトゲートウェイのルーティング削除  
ip route del default, ip r del default
* スタティックルーティングの追加  
sudo ip route add 192.168.2.0/24 via 192.168.1.254 dev eth0  
sudo ip route add 192.168.2.100/32 via 192.168.1.254 dev eth0  
* スタティックルーティングの削除  
sudo ip route del 192.168.2.0/24  
sudo ip route del 192.168.2.100/32  
* ネットワークデバイスごとの送受信バイト/パケット数など一覧  
ip -s l (統計情報)  
* ARP一覧(arp -an)  
ip neighbour, ip n (arp)  
* Listenポート表示(netstat -ln --tcp)  
ss -tln  (TCP)  
ss -uln  (UDP)  
* ESTABLISH なポート表示(netstat -en --tcp)  
ss -tn  
ss -tun  
ss -tunp  
ss -tn state established  
ss -tan | grep ESTAB  
* TCP の状態  
ss -ant
* UDP の状態  
ss -anu
* nmcli系  
デバイス一覧: nmcli d  
デバイスの詳細情報: nmcli d sh enp1s0  
接続一覧: nmcli c  
* ネットワークデバイスの up/down(ifconfig up/down)  
enp1s0 を up: ip l set enp1s0 up  
enp1s0 を down: ip l set enp1s0 down  
* ホスト情報(ホスト名、OSバージョン、カーネル情報など)  
hostnamectl  
* ホスト名変更  
hostnamectl set-hostname <新しいホスト名>

### firewall系
* 一覧表示
  * 解放された**サービス**の一覧を表示  
`firewall-cmd --list-services [--zone=public] [--permanent]`
    * サービスで定義されたポート番号を確認  
      /usr/lib/firewalld/services/ ディレクトリにある XML ファイルの内容を確認する  
    * 登録可能なサービス名を一覧表示  
      `firewall-cmd --get-services`
  * 解放された**ポート**の一覧を表示  
`firewall-cmd --list-ports [--zone=public] [--permanent]`
  * 設定一覧を表示する  
`firewall-cmd --list-all`
  * すべてのゾーンを表示する  
`firewall-cmd --list-all-zones`
  * 現在有効なゾーンの名前を表示する  
`firewall-cmd --get-active-zone`
  * デフォルトのゾーン名を表示する  
`firewall-cmd --get-default-zone`
  * iptables を使って従来の(iptablesの)形式で表示  
`iptables -nL`
  * public ゾーンの input に対する設定だけに絞り込んで表示  
`iptables -nL IN_public_allow`
* 許可
  * コマンドでサービスを許可  
`firewall-cmd --zone=public --add-service=http --permanent`
    * firewall-cmd でサービス/ポートを追加すると、`firewall-cmd --reload` するまで  
`firewall-cmd --list-xxx` に表示されない。  
`firewall-cmd --list-xxx --permanent` には表示される。
  * コマンドでポートを許可  
`firewall-cmd --zone=public --add-port=443/tcp --permanent`
  * 手っ取り早く**永続的**に解放するサービス/ポートを追加  
    vim /etc/firewalld/zones/public.xml  
    ```bash
    <service name="cifs"/>
    <port protocol="tcp" port="445" />
    ```  
  * --add-port や public.xml 編集後、リロードで反映  
    `firewall-cmd --reload`
* 禁止  
  * 送信先IP、ポートを指定して禁止する例  
    firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 1 -m tcp -p tcp -d 192.168.1.1 --dport 80 -j DROP  
    解除する時は --add-rule を --remove-rule にして、残りのパラメーターはそのままで実行

### サービス系
* サービスの一覧を表示(chkconfig --list)  
systemctl list-unit-files  
systemctl list-units  
systemctl list-unit-files --type=service  
systemctl list-units --type=service  
* サービス起動・停止・再起動(service xxx {start|stop|restart}  
service httpd start => systemctl start httpd.service  
service httpd stop => systemctl stop httpd.service  
service httpd restart => systemctl restart httpd.service  
* サービスの強制終了(kill -9)  
systemctl kill -s 9 httpd.service
* サービス自動起動の確認  
chkconfig --list httpd => systemctl is-enabled httpd.service
* サービス自動起動の有効化・無効化(chkconfig xxx {on|off})  
chkconfig httpd on => systemctl enable httpd.service  
chkconfig httpd off => systemctl disable httpd.service  
* ログの確認  
journalctl -u httpd.service  
* dmesg の確認(dmesg)  
journalctl --dmesg  

#### サービスを作成
1. サービス定義ファイルを作成  
vim /etc/systemd/system/hoge.service  
   ```
   [Unit]
   Description = Hoge!
   # xxx が動いてからこのサービスを起動する
   After = xxx.service xxx.target
   # xxx より前にこのサービスを起動する
   Before = xxx.service
   
   [Service]
   ExecStart = /usr/local/bin/hoge.sh
   ExecStop = /usr/bin/kill $MAINPID
   Restart = [no|always]
   Type = [simple|forking]
   
   [Install]
   WantedBy = multi-user.target
   ```
1. systemctl に認識されたか確認  
`systemctl list-unit-files --type=service | grep hoge`
1. 有効化  
`systemctl enable hoge.service`
1. /etc/systemd/system/hoge.service を編集した場合は再読み込み  
`systemctl daemon-reload`
* サービス実行状態を表示  
systemctl status hoge.service
* サービス実行状態全体を表示  
systemctl -l status hoge.service
* ログ表示  
journalctl -xe
* 依存しているサービスを表示  
systemctl list-dependencies --after hoge.service
* 依存されているサービスを表示  
systemctl list-dependencies --before hoge.service

### その他設定

* タイムゾーン設定  
`timedatectl set-timezone Asia/Tokyo`
* ロケール設定  
`localectl set-locale LANG=ja_JP.utf8`
* /etc/yum.repos.d/CentOS-Base.repo に指定するむ CentOS 7.5 の baseurl 例  
  ```
  http://ftp.riken.jp/Linux/centos-vault/7.5.1804/os/x86_64/
  http://ftp.riken.jp/Linux/centos-vault/7.5.1804/updates/x86_64/
  http://ftp.riken.jp/Linux/centos-vault/7.5.1804/extras/x86_64/
  http://ftp.riken.jp/Linux/centos-vault/7.5.1804/centosplus/x86_64/
  ```




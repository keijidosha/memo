# Linux: ネットワーク

## ネットワークインターフェースの通信速度を設定
* 100Mbps に設定する場合  
    ```
    ifconfig eth0 media 100baseTX
    ```
    または /etc/conf.modules に
    ```
    alias eth0 tulip
    options tulip options=100baseTX
    ```
    と記述。  
    (参考) http://www.ecn.purdue.edu/~laird/Linux/LNE100TX/readme.txt
* オートネゴシエーションにする場合  
    ```
    ifconfig eth0 media autoselect
    ```
* xinetd を使って root だけが Listen できる 1023 以下のポートを、一般ユーザが待機できる 1024 以降のポートにリダイレクト  
例:Tomcat 用
    ```
    service tomcat
    {
           socket_type     = stream
           protocol        = tcp
           user            = root
           wait            = no
           port            = 80
           redirect        = localhost 8080
           disable         = no
        }
    ```
* RedHat, Fedore のファイアウォール設定(setup コマンド)が保存されるファイル  
    ```
    /etc/sysconfig/iptables
    /etc/sysconfig/system-config-securitylevel
    ```
* nslookup によるゾーン転送設定の確認  
    ```
    nslookup
    server xxx.xxx.xxx.xxx (DNSサーバのIPアドレス)
    ls -d <ドメイン名> (ゾーン転送を確認するドメイン名)
    ls -d xxx.xxx.xxx.in-addr.arpa. (逆引きのゾーン転送を確認するIPアドレス - 後ろから逆に指定)
    ```
  Windows の nslookup ではうまく動きますが、最近の Linux では ls -d はサポートされていないようです。
* コネクション確立後に行う TCP 通信でのデフォルトリトライ回数を設定  
  * RedHat系  
  /proc/sys/net/ipv4/tcp_retries2 に値を設定(デフォルト15回)  
  (例) 3回に変更する場合
    ```
    echo 3 > /proc/sys/net/ipv4/tcp_retries2
    ```

## iptables
* iptables の設定一覧を表示する。  
    ```
    iptables -L -n
    ```
* iptables の設定一覧を行番号付きで表示する。  
    ```
    iptables -nL --line-numbers
    ```
* iptables の統計情報を表示する。  
    ```
    iptables -L -n -v
    ```
* iptables の NAT 一覧を表示する。  
    ```
    iptables -L -n -v -t nat
    ```
* iptables の conntrack エントリを表示する。  
    ```
    cat /proc/net/ip_conntrack
    ```
* setup コマンドがない環境でファイアウォールで許可するポートを追加  
  ポート 8080 を追加する場合~ /etc/sysconfig/iptables に
    ```
    -A RH-Firewall-1-INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT
    ```
  という行を追加。  
  /etc/sysconfig/system-config-securitylevel に –port=8080:tcp

## IPSec
* IPSec(FreeS/WAN)の接続状況確認  
    ```
    ipsec look
    ipsec auto --status
    ```

## netstat
* netstat でプロセスID/プロセス名を表示  
    ```
    netstat -anp
    ```

## route
* デフォルトゲートウェイを 192.168.1.254 に設定  
    ```
    route add -net default gw 192.168.1.254
    ```
* 192.168.2.1/24 へのゲートウェイを 192.168.1.254 に設定  
    ```
    route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.1.254
    ```

## DHCP
* /etc/dhclient-eth0.conf の記述内容  
    ```
    send host-name ”<hostname>”;
    ```

## ethtool, nmcli(ネットワークチューニング/パケットドロップ/...)

* パケットのドロップの有無を確認
  ```
  ethtool -S eth0
  ```
* バッファサイズを確認
  ```
  ethtool -g eth0
  ```
* バッファサイズを調整
  * nmcli
    * プロファイル名を確認
      ```
      nmcli connection show
      NAME  UUID             TYPE      DEVICE 
      eth0  xxx-xxx-xxx-xxx  ethernet  eth0
      ```
    * プロファイルの内容を確認
      ```
      nmcli  connection show eth0
      ```
    * 受信用リングバッファサイズを設定
      ```
      nmcli connection modify eth0 ethtool.ring-rx 4096
      ```
    * 送信用リングバッファサイズを設定
      ```
      nmcli connection modify eth0 ethtool.ring-tx 4096
      ```
    * 変更した設定を反映
      ```
      nmcli connection up eth0
      ```
  * ethtool  
    再起動するとリセットされることに注意
    * 受信用リングバッファサイズを設定
      ```
      ethtool -G eth0 rx 512
      ```
    * 送信用リングバッファサイズを設定
      ```
      ethtool -G eth0 tx 512
      ```


## vnc
### VNCサーバ起動
1. vncserver を起動  
    ```
    vncserver :1
    ```
  (:1 はディスプレイ番号)
  初回は VNC クライアントから接続する時に使うパスワードの入力を求めてくるので、パスワード入力して設定。  
  するとホームディレクトリに .vnc/passwd ファイルが作成されます。  
  このファイルを削除すると、パスワードをリセットしてパスワードを変更することができるかも。
2. Windowsから接続  
vncviewer を起動して '<ホスト名>:1' または '<IPアドレス>:1' に対して接続
1. vncserver を停止
    ```
    vncserver -kill :1  
    ```
デフォルトでは twn が起動しますので、標準のウィンドウマネージャが起動するように設定します。
1. cd ~
1. ln -s /etc/X11/xinit/xinitrc .xinitrc
1. cd .vnc
1. mv xstartup xstartup.org
1. ln -s ../.xinitrc xstartup

#### VNC 経由で Oracle セットアップを行うと、インストーラから X に接続できない(unable to open display)
* ssh 接続し、vncserver を root で起動してから vnc 接続し、vnc からターミナルを開いて su - oracle でユーザを切り替えてからインストーラを起動するとうまくいかなかった。  
* ssh 接続した後、su - oracle で oracle ユーザに切り替えてから、vncserver を起動するとうまくいきました。

## Advanced [es] をモデムとして使う
* Product ID を確認  
`view /proc/bus/usb/devices`
* モデムを認識  
`sudo modprobe ipaq vendor=0x04dd product=0x91ac`
* デバイスとして認識されているか確認  
`ls /dev/ttyUSB*`
### おもに kppp を使った場合の設定
1. モデム初期化コマンドを設定  
1. 「モデム」タブから「モデムコマンド」ボタンをクリック
1. 「初期化文字列2」に次のモデムコマンドを指定  
`AT&FE0V1Q0&C1&D2&S0`
1. 「モデムのボリューム」を最小に設定
1. モデムの音量がオフの時のモデムコマンドをクリア
* すぐ切れてしまう場合は /etc/ppp/options の次の行をコメントアウトしてみる。  
    ```
    lcp-echo-interval
    lcp-echo-failure
    ```


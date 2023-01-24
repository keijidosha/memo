# Linux

## 操作

* シングルユーザモードで起動。
  1. 起動時に表示されるカウントダウンの画面で何かキーを押す。
  1. パーティションの一覧が表示されるので e を押す。
  1. xxxKernelxxx を選択して e(edit)を押す。
  1. パラメータの最後に single を入力して Enter を押す。
  1. b(boot)を押して起動する。  
この時 /(ルートディレクトリ)はリードオンリーでマウントされています。 そのため、設定ファイルの編集などはできません。 読み書き可能にするためには、再マウントします。  
`mount -n -o remount,rw /dev/hda1`
* knoppix を使ってメンテナンス目的で起動する場合の、起動パラメータ  
knoppix 2 noswap vga=normal
* ps コマンドをコンソールの右端で切れないように表示する。  
ps axww
* ps コマンドを環境変数も含めた長い表示形式で、ツリー表示する。  
ps alefxww
* 起動時に実行するサービスを設定・確認する。  
  * TurboLinux の場合
    turboservice
  * RedHat の場合
    ntsysv
* リスンしているポートを表示する。  
nmap localhost
* ランレベルごとに実行されるサービスの一覧を表示、設定  
  * 表示  
    chkconfig –list
  * 設定  
    chkconfig –level <ランレベル> <サービス名(例:ssh)> on|off
* inode の消費状況を表示  
df -i
* sudo で実行するコマンドにリダイレクトを使う。  
sh -c パラメータを使います。  
sudo sh -c "command xxx > xxx.log"  
* USBメモリを使う。
  ```
  mkdir /media/usbfm
  mount -t vfat /dev/sda1 /media/usbfm
  ```
  または
  ```
  mount -t vfat /dev/sda2 /media/usbfm
  ```
  デバイス名は USBメモリを挿した直後に dmesg で確認することができます。
* マウントを解除
  ```
  umount /media/usbfm
  eject /dev/sda2
  ```
* DVD+/-RWをフォーマット  
`dvd+rw-format -full /dev/dvdwriter`  
または  
`dvd+rw-format -blank=full /dev/dvdwriter`
* DVD+/-RWにデータを追記  
root で行う必要があります。(sudo では実行できません。)  
`growisofs -Z /dev/dvdwriter -R -J <ファイル名> または <ディレクトリ名>`
* 複数に分割されたファイルを結合  
(例) splitedfile.001, splitedfile.002, splitedfile.003 を originalfile.dat に結合  
`cat splitedfile.* > originalfile.dat`
* 複数のディレクトリを一気に作成する。  
例えば RPM 作成用ディレクトリを一気に作成する場合。  
`mkdir -p ~/rpm/{BUILD,SOURCES,SPECS,SRPMS,RPMS/{i386,i586,i686,noarch}}`

### tarコマンド

* tar ファイルに圧縮後、圧縮したファイルを削除。  
`tar zcvf xxx.tar.gz –remove-files *.txt`
* tar ファイルに格納されたファイルの内容を画面に表示  
  * すべてのファイルを画面に表示  
  `tar zxOf xxx.tar.gz`
  * 特定のファイルを画面に表示  
  `tar zxOf xxx.tar.gz foo.txt`  
  (ワイルドカード指定はダブルクォートで囲む)  
  `tar zxOf xxx.tar.gz “*.txt”`
* vi, less を使って tar に格納されたファイルを表示
  ```
  tar zxOf xxx.tar.gz “*.txt” | less
  tar zxOf xxx.tar.gz “*.txt” | view -
  tar zxOf xxx.tar.gz “*.txt” | vi -R -
  ```

### rsync で指定するパス

* (正解) /var/log が /opt/log にコピーされます。  
`rsync -auv /var/log/ /opt/log/`
* (間違い) /var/log が /opt/log/log としてコピーされます。  
`rsync -auv /var/log /opt/log/`
* (間違い) /var/log が /opt/log/log としてコピーされます。  
`rsync -auv /var/log /opt/log`
* (正解) /var/log が /opt/log にコピーされます。  
`rsync -auv /var/log /opt`

### ラベル

* ディスクパーティションにラベルを付ける。  
`e2label <device> <ラベル名>`  
(例)  
`e2label /dev/sda3 mydata`
* パーティションのラベルを表示。  
`e2label <device>`  
(例)  
`e2label /dev/sda3`
* ラベルを指定してパーティションをマウント  
`mount -t <fstype> -L <ラベル> <マウント先>`  
(例)  
`mount -t ext3 -L data /data`
* パーティションのバックアップ  
`dd if=/dev/sda1 bs=8129 | gzip > host1_xvda-sda1.gz`  
この時は Xen 仮想VM 用に割り当てたパーティションをバックアップしました。


### shell

* kshell 等で補完機能を使えるようにする。  
`set -o emacs`

#### shell script

* ある特定のファイルが存在する時だけ処理を実行する。  
  find を実行した結果、見つかったファイルの行数が 1行以上の場合だけ、条件が成立し、処理を実行します。
  ```
  if [ `find /tmp/foo -name '*.txt' -type f | wc -l` -gt 0 ] ; then
      # 処理
  fi
  ```
* ファイル名を正規表現で一括変換する  
(例) 年月日の間にピリオドを挿入する。つまり、20100301.txt を 2010.03.01.txt に変換する。  
`for file in 20*; do mv $file `echo $file | sed -e "s/\([0-9]\{4\}\)\([0-9]\{2\}\)\([0-9]\{2\}\)/\1\.\2\.\3/"`; done`  
echoで実行内容を表示し、正しく変換できているのを確認してから実行すると良い。  
`for file in 20*; do echo `echo $file | sed -e "s/\([0-9]\{4\}\)\([0-9]\{2\}\)\([0-9]\{2\}\)/\1\.\2\.\3/"`; done`
* 前日を取得  
`date –date '1 day ago'`  
(例) 前日の日付を8桁の数字でシェル変数 yesterday にセット   
`yesterday=`date -d '1 day ago' +%Y%m%d``
* for を使って複数のファイルに対する操作を実行  
(例) 拡張子を doc から txt に変更  
`for fname in *.doc; do mv $fname ${fname%.doc}.txt; done`  
(例) すべての JAR ファイルに署名  
`for jar in *.jar; do jarsigner -keystore /mnt/foo.key -storepass passs -keypass passk -signedjar sign/$jar $jar aliasname; done`


## Java

* RedHat Linux 9 で JDK 1.3.1 を使う場合。  
環境変数 LD_ASSUME_KERNEL=2.2.5 を指定。

## Network


* ネットワークインターフェースの通信速度を設定
  * 100Mbps に設定する場合  
    `ifconfig eth0 media 100baseTX`  
    または /etc/conf.modules に  
    ```
    alias eth0 tulip
    options tulip options=100baseTX
    ```
    と記述。
    (参考) http://www.ecn.purdue.edu/~laird/Linux/LNE100TX/readme.txt
  * オートネゴシエーションにする場合  
    `ifconfig eth0 media autoselect`
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
    `echo 3 > /proc/sys/net/ipv4/tcp_retries2`

### iptables

* iptables の設定一覧を表示する。  
`iptables -L -n`
* iptables の設定一覧を行番号付きで表示する。  
`iptables -nL --line-numbers`
* iptables の統計情報を表示する。  
`iptables -L -n -v`
* iptables の NAT 一覧を表示する。  
`iptables -L -n -v -t nat`
* iptables の conntrack エントリを表示する。  
`cat /proc/net/ip_conntrack`
* setup コマンドがない環境でファイアウォールで許可するポートを追加  
ポート 8080 を追加する場合~ /etc/sysconfig/iptables に  
`-A RH-Firewall-1-INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT`  
という行を追加。~ /etc/sysconfig/system-config-securitylevel に –port=8080:tcp

### IPSec

* IPSec(FreeS/WAN)の接続状況確認  
  ```
  ipsec look
  ipsec auto --status
  ```

### netstat
* netstat でプロセスID/プロセス名を表示  
`netstat -anp`

### ルーティングテーブル

* デフォルトゲートウェイを 192.168.1.254 に設定  
`route add -net default gw 192.168.1.254`
* 192.168.2.1/24 へのゲートウェイを 192.168.1.254 に設定  
`route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.1.254`

### DHCP

* /etc/dhclient-eth0.conf の記述内容  
`send host-name ”<hostname>”;`

### Advanced [es] をモデムとして使う

* Product ID を確認  
`view /proc/bus/usb/devices`
* モデムを認識  
`sudo modprobe ipaq vendor=0x04dd product=0x91ac`
* デバイスとして認識されているか確認  
`ls /dev/ttyUSB*`

おもに kppp を使った場合の設定  
1. モデム初期化コマンドを設定  
「モデム」タブから「モデムコマンド」ボタンをクリック  
「初期化文字列2」に次のモデムコマンドを指定  
`AT&FE0V1Q0&C1&D2&S0`  
1. 「モデムのボリューム」を最小に設定
1. モデムの音量がオフの時のモデムコマンドをクリア

すぐ切れてしまう場合は /etc/ppp/options の次の行をコメントアウトしてみる。  
1. lcp-echo-interval
1. lcp-echo-failure

### vnc

* VNCサーバ起動
  1. vncserver を起動  
    `vncserver :1`  
    (:1 はディスプレイ番号)  
    初回は VNC クライアントから接続する時に使うパスワードの入力を求めてくるので、パスワード入力して設定。  
    するとホームディレクトリに .vnc/passwd ファイルが作成されます。  
    \# このファイルを削除すると、パスワードをリセットしてパスワードを変更することができるかも。  
  1. Windowsから接続  
     vncviewer を起動して '<ホスト名>:1' または '<IPアドレス>:1' に対して接続
  1. vncserver を停止  
    `vncserver -kill :1`

デフォルトでは twn が起動しますので、標準のウィンドウマネージャが起動するように設定します。  
1. cd ~
1. ln -s /etc/X11/xinit/xinitrc .xinitrc
1. cd .vnc
1. mv xstartup xstartup.org
1. ln -s ../.xinitrc xstartup

* VNC 経由で Oracle セットアップを行うと、インストーラから X に接続できない(unable to open display)
  * ssh 接続し、vncserver を root で起動してから vnc 接続し、vnc からターミナルを開いて su - oracle でユーザを切り替えてからインストーラを起動するとうまくいかなかった。
  * ssh 接続した後、su - oracle で oracle ユーザに切り替えてから、vncserver を起動するとうまくいきました。

### ssh

* ssh-agent を使って、秘密鍵を追加
  ```
  eval `ssh-agent`
  ssh-add <秘密鍵のパス>
  ```

## 環境/設定

### date

* 時刻を設定  
`date -s 18:00`
* 2009.04.01 00:00:00 に設定  
`date -s 090401`  
または  
`date -s "2009/04/01 00:00:00"`
* 2009.04.01 15:30:00 に設定  
`date -s "090401 15:30:00"`  
または  
`date -s "2009/04/01 15:30:00"`

(参考)
http://www.atmarkit.co.jp/flinux/rensai/eeepc01/eeepc01b.html  
http://itpro.nikkeibp.co.jp/article/COLUMN/20060228/231082/

### CD

* 使用可能なファイルシステムの一覧を表示  
`cat /proc/filesystems`
* CD のマウント指定を /etc/fstab に記述(CentOS 4.4を参考)  
`mkdir /cdrom`  
を実行して、下記内容を /etc/fstab に追記。  
`/dev/cdrom /cdrom auto pamconsole,fscontext=system_u:object_r:removable_t,exec,user,ro,noauto,managed 0 0`
  * user  
    一般ユーザもマウント可能
  * ro  
    読み込み専用でマウント  
    read-only のメッセージが表示されなくなる。
  * noauto
    OS 起動時にはマウントしない
* CD をマウントする時、大文字を小文字にマップ(変換されないようにする)。  
map=off というオプションを指定してください。  
`mount -o map=off /mnt/cdrom`

### 時刻

* タイムゾーンを日本(+0900)に設定  
`ln -s /usr/share/zoneinfo/Japan /etc/localtime`
* ハードウェアクロックをシステムクロックに同期。  
`hwclock –systohc`
* 経過秒数を日付・時刻に変換して表示  
`perl -e '$dt = localtime <経過秒数>; print “$dt\n”;'`

### RPM

* Fedora Core で「kernel-headers is needed by xxx」というメッセージが表示されるが、Fedore Core には「kernel-headers」の RPM が含まれていない。  
Fedora Core に gcc を入れようとすると、依存関係から kernel-headers が必要となりますが。Fedora Core には kernel-headers の RPM が含まれていません。  
代わりに glibc-kernheaders を使うとうまくいきます。
* 最小セットアップした CentOS 4.4 から不要と思われる RPM パッケージを削除  
`rpm -e Canna Canna-libs FreeWnn FreeWnn-libs GConf2 ORBit2 OpenIPMI OpenIPMI-libs alsa-lib anacron aspell aspell-en audiofile bluez-bluefw bluez-hcidump bluez-libs bluez-utils chkfontpath cups cups-libs desktop-file-utils esound fontconfig freetype gnome-keyring gnome-mime-data gnome-vfs2 iiimf-csconv iiimf-docs iiimf-le-canna iiimf-libs iiimf-server irda-utils isdn4k-utils jisksp14 jisksp16-1990 kon2 kon2-fonts lftp lha libbonoboui libglade2 libgnome libgnomecanvas libgnomeui libjpeg libpng pcmcia-cs talk tcsh ttfonts-ja wireless-tools words xorg-x11-Mesa-libGL xorg-x11-font-utils xorg-x11-libs xorg-x11-xfs yp-tools ypbind libbonobo pango gtk2 ttmkfdir pygtk2-libglade system-switch-im redhat-lsb redhat-menus libtiff startup-notification pygtk2 NetworkManager stunnel htmlview pinfo`

### スワップファイル

* swap ファイルを 1GB追加。
  ```
  cd /var
  # swap ファイル用ディレクトリを作成
  mkdir /swap
  cd swap
  # swap 用ファイルを作成
  dd if=/dev/zero of=swap1 bs=1024 count=1024000
  
  読み込んだブロック数は 1024000+0
  書き込んだブロック数は 1024000+0
  # 作成したファイルを swap ファイルとして初期化
  /sbin/mkswap swap1
  Setting up swapspace version 1, size = 104853504 kB # 作成した swap ファイルを優先度30でアクティブに設定
  /sbin/swapon swap1 -p 30
  # swap ファイルの状況を表示
  /sbin/swapon -s
  
  Filename                                Type            Size    Used    Priority
  /dev/mapper/VolGroup00-LogVol01         partition       524280  0       -1
  /var/swap/swap1                         file            599992  0       30
  # swap ファイルをはずしてみる
  /sbin/swapoff -a swap1
  # swap ファイルの状況を表示してみるが、何も表示されない
  /sbin/swapon -s
  # 再び swap ファイルをアクティブに
  /sbin/swapon swap1 -p 30
  ```


* bash を使っている時、重複したコマンドを実行履歴に格納しない。  
次の行を ~/.bash_profile に追加  
`HISTCONTROL=ignoredups`
* ctrl + r でコマンド履歴を戻りすぎた時、ctrl + s で逆方向に進める設定  
次の行を ~/.bash_profile に追加  
`stty stop undef`

* /etc/rc.d/init.d 配下にある、自作の起動/停止用スクリプトを service に登録  
  1. 次の行をスクリプトに記述しておく  
     `# chkconfig: 345 99 01 -345`
     * 345  
       on にする runlevel
     * 99  
       on にする時用のシンボリックリンクの番号 ⇒ 起動する順序
     * 01  
       off にする時用のシンボリックリンクの番号 ⇒ 停止する順序~  
  1. chkconfig –add xxx

SELinux

有効/無効の設定

-有効にする~ SELINUX=enforcing~ -無効にするがメッセージは制限に引っかかったところのメッセージは表示する~ SELINUX=permissive~ -無効にする~ SELINUX=disabled~

cron

曜日を指定

0:Sun,1:Mon,2:Tue,3:Wed,4:Thu,5:Fri,6:Sat,7:Sun

一定間隔で実行(5分ごとに実行)

#minute hour mday month wday command */5 * * * * /bin/touch xxx

「0,5,10,15,20,25,30,35,40,45,50,55」と同じ。

ある期間だけ実行(5分から10分の間だけ毎分実行)

#minute hour mday month wday command 5-10 * * * * /bin/touch xxx

xfs

状況表示

xfs_db -c frag -r /dev/sda2

最適化

xfs_fsr /dev/sda2

xfs_fsr 実行後、xfs_db で状況を表示してもあまり改善されていませんでした。 が、再起動してからもう一度 xfs_db を実行すると、「fragmentation factor 0.00%」になっていました。

CentOS で xfs をサポート

ダウンロード

ftp://ftp.riken.jp/Linux/centos/4/centosplus/i386/RPMS/ から次のファイルをダウンロード。 -kernel-2.6.9-42.0.10.plus.c4.i686.rpm -kernel-module-xfs-2.6.9-42.0.10.plus.c4-0.2-1.i686.rpm -kernel-devel-2.6.9-42.0.10.plus.c4.i686.rpm -xfsdump-2.2.42-1.c4.i386.rpm -xfsprogs-2.8.16-1.c4.i386.rpm -xfsprogs-devel-2.8.16-1.c4.i386.rpm -dmapi-2.2.5-1.c4.i386.rpm

インストール

上記ファイルをインストール。

フォーマット

3番目の IDE ドライブの最初のパーティションを xfs で強制的にフォーマット。 mkfs -t xfs -f /dev/hdc1 パーティションがすでに別の形式でフォーマットされている場合、-f を付けないとエラーになります。

マウント

次のコマンドを入力して、マウントできるか確認します。 mount -t xfs /dev/hdc1 /opt

fstab を編集

/etc/fstab に次の行を追加して、ブート時に自動的にマウントされるようにします。 /dev/hdc1 /opt xfs defaults 1 2

Fedora Core 6 で xfsdump をインストール

Fedora Core 6 の標準パッケージには xfsdump が含まれていません。 そこで yum を使ってインストールする必要があります。 yum install xfsdump

reiserfs

Fedora Core 6 で reiserfs を有効にする。

rpm -Uvh reiserfs-utils-xxx.rpm

/etc/fstab に reiserfs を指定して、起動時にマウントするようにしておくと、起動時にエラーが発生。

selinux を無効にすると起動できるようになりました。

起動時のカーネルオプションに enforcing=0 を指定。~ または /etc/sysconfig/selinux を編集。

CentOS5, Fedora Core 6 を最小セットアップした後で入れておくと便利なパッケージ

-ネットワーク系~ dhclient~ openssh~ openssh-server~ openssh-client~ bind-utils~ ~ -開発系~ gcc~ cpp~ glibc-devel~ kernel-header~ kernel-devel~ ~ -未分類~

プロセスの優先度を変更

プロセスID を指定 renice <優先度> -p <プロセスID>

ユーザ名を指定 renice <優先度> -u <ユーザ名>

:優先度|-20(最優先) ～ 19(一番後回し) の間で指定

バイナリ(実行)ファイルの依存関係を表示

ldd <ファイル名>

sudo を使って特定のユーザになる権限だけを与える。

su でユーザを切り替えるだけの shll script を作成。

cat /usr/local/bin/sufoo.sh
#!/bin/sh
 
su - foo;
/etc/group に特定のユーザになる権限を与えたいグループとメンバーを登録。

tail /etc/group
foousers:x:300:user1,user2,user3
visudo で /etc/sudoers に権限を与えたいグループと、作成した shell script を登録

%foousers  ALL=(root)      NOPASSWD: /usr/local/bin/sufoo.sh
メモリ

OOM Killer を無効に設定する。

echo 0 > /proc/sys/vm/oom-kill

OOM Killer にこのプロセスは対象外であることを通知する。

echo -17 > /proc/〈PID〉/oom_adj

malloc の overcommit を無効にする。

カーネルパラメータを次のように設定。 vm.overcommit_memory=2

overcommit を無効にした時の、全プロセスで malloc 可能なメモリサイズの計算式

swap領域のサイズ[MB] + (物理メモリ量[MB] * overcommit_ratio / 100)

メモリを占有している順番でプロセスを表示

ps -axl –sort -vsize または top を実行して、'M'(大文字のM)をタイプ

論理ボリューム

ボリュームグループをマウント

ボリュームグループを検索 # lvm vgscan Reading all physical volumes. This may take a while… Found volume group “VolGroup00” using metadata type lvm2

ボリュームグループを使用可に設定 #lvm vgchange -a y 2 logical volume(s) in volume group “VolGroup00” now active

論理ボリュームをマウント # mount /dev/VolGroup00/LogVol00 /mnt/sda1

ボリュームグループの名前を変更

ボリュームグループを使用不可に設定 lvm vgchange -an <ボリュームグループ名>

ボリュームグループをリネーム lvm vgrename <旧ボリュームグループ名> <新ボリュームグループ名>

エンコーディング

CentOS 4 以降でエンコーディングに Shift-JIS を使えるように設定

RedHat, CentOS 4 以降では使用可能なエンコーディングにシフトJIS が含まれていません。
使用可能なエンコーディングは次のコマンドで確認できます。

locale -a | grep ja
そこで次のコマンドを実行してシフトJIS を使えるようにします。

localedef -f SHIFT_JIS -i ja_JP ja_JP.SJIS
UTF-8 の場合

localedef -f UTF-8 -i ja_JP ja_JP.utf8
カーネルモジュール

modules.dep を(再)作成

mkdir -p /lib/modules/`uname -r`
depmod
OpenSSL

証明書がプリインストールされているファイル・ディレクトリ

CentOS
/etc/pki/tls/certs/ca-bundle.crt
Ubuntu
/etc/ssl/certs
セットアップ

RPM

httpd の RPM をインストールしようとすると、/etc/mime.types が必要と表示される。(CentOS 5)

mailcap-xxx.rpm に含まれています。

yum

CentOS-Testing を使ってモジュールを update

CentOS 4.x の場合~ +CentOS-Testing.repoをダウンロード~ cd /etc/yum.repos.d wget http://dev.centos.org/centos/4/CentOS-Testing.repo +CentOS-Testing.repo を編集して enabled を 1 に設定~ enabled=1 +今回は ruby を 1.8.1 ⇒ 1.8.5 に更新~ yum update ruby +CentOS-Testing.repo の enabled を 0 に戻しておく~ enabled=0

checkinstall(SPEC ファイルなしで PRM パッケージを作成)

http://dag.wieers.com/rpm/packages/checkinstall/

確認する

プロセス番号と実行ファイル

実行ファイルからプロセス番号をしらべる
fuser (実行ファイル)
プロセス番号から実行ファイルをしらべる
ls -l /proc/(プロセス番号)/exe
プロセス番号と実行中のファイルの関連をしらべる
pstree -p, ps aux , top または ls -lv /proc/[0-9]*/exe
ファイル/ポート/ファイルシステムとプロセス

fuser -v (ファイル|ディレクトリ)
プロセス番号にはファイルへのアクセス形式を表すマークがつく.
c(カレントディレクトリ)
e(実行ファイル)
f(オープンファイル)
r(ルートディレクトリ)
m(mmap されたファイルか共有ライブラリ)
fuser -v telnet/tcp
TELNET ポートにアクセスしているプロセス. /etc/servicesを参照.
fuser -mv (マウントポイント)
ファイルシステム上のファイルにアクセスしているすべてのプロセス.
umount でデバイスを使用中のときに確認に使える.
プロセスが使用(オープン)しているファイル

使用中のファイル/socket/pipe をしらべる.

ls -lv /proc/(プロセス番号)/fd |sed -n 's/^.* -> //p'
(ファイルのみを表示する場合)

ls -lv /proc/(プロセス番号)/fd |sed -n 's:^.* -> /:/:p'
shell

シェル変数の展開

${変数名#word}
変数から値を取り出し、その先頭部分がwordと一致した場合その部分を取り除きます。
${変数名%word}
変数から値を取り出し、その後方部分がwordと一致した場合その部分を取り除きます。
${変数名//pattern/word}
変数から値を取り出し、patternとマッチする部分をwordで置換します。
Tips

initrd の中身を展開して見る

mkdir /tmp/initrd
cd /tmp/initrd
した後

gzip -dc /boot/initrd-2.6.xx-xxx.img | cpio -id
または

zcat /boot/initrd-2.6.xx-xxx.img | cpio -i -c
展開したファイルを編集してブートイメージを作成する場合は

find . | cpio --quiet -c -o | gzip -c > /boot/initrd-2.6.xx-xxx_2.img
scp でスペースを含むディレクトリに対してコピーする

(例) vmware 仮想イメージをディレクトリごとコピーする場合

scp -pr centos root@172.16.1.1":/var/lib/vmware/Virtual\ Machines/
トラブルシューティング

knoppix の CD から起動して、Volume Group で作成したパーティションを認識させる。

knoppix をダウンロード
http://www.ring.gr.jp/archives/linux/knoppix/iso/
CDイメージから knoppix の CD を作成
knoppix の CD から起動
shell を起動
su - で root になる
次のコマンドを実行します。

# lvm vgscan
Reading all physical volumes. This may take a while...
Found volume group "VolGroup00" using metadata type lvm2
 
#lvm vgchange -a y
2 logical volume(s) in volume group "VolGroup00" now active
 
# mount /dev/VolGroup00/LogVol00 /mnt/sda1
AMD Athlon 64 X2 に Kernel 2.6.x をセットアップすると、「kernel panic - not syncing:PCI-DMA:high address but no IOMMU.」というエラーが発生。

起動時のカーネルパラメータに次のいずれかを指定すると回避できるかもしれません。~ -acpi=off~ -iommu=off~ -iommu=soft~

grub.conf の例~

default=0~ timeout=5~ splashimage=(hd0,0)/grub/splash.xpm.gz~ hiddenmenu~ title CentOS (2.6.18-8.el5)~ &nbsp;&nbsp;root (hd0,0)~ &nbsp;&nbsp;kernel /vmlinuz-2.6.18-8.el5 ro root=/dev/VolGroup00/LogVol00 &color(blue){acpi=off};~ &nbsp;&nbsp;initrd /initrd-2.6.18-8.el5.img~

CentOS, Fedora でシャットダウン後、電源が切れない

シャットダウン実行後、最後に電源が切れないままになるため、その後 BIOS の自動起動や WOL による起動ができない。~ この時コンソールに表示されていたメッセージ~ Shutdown: hdb Power down. acpi_power_off called このメッセージが表示された状態で止まっていました。~ chkconfig で実行しているデーモンを確認すると、acpid が on になっていましたので、acpid を実行しないようにすると、シャットダウン後、電源が切れるようになりました。 chkconfig acpid off /etc/grub.conf のカーネルパラメータに acpi=off を指定する、という方法もあるらしい。~ acpi経由で電源を切ろうとしてうまく動いていない?~ ※この時は Dell SC430(Intel Pentium D) + CentOS5.1 の組み合わせでしたが、他のハードや Fedora でも発生している模様。

CentOS5.x を yum update すると、起動にかなり時間がかかるようになった。

現象~ -DHCP設定された eth0 の IPアドレス取得にかなり時間がかかる。~ -syslog が起動しないため、/var/log/message が出力されてない。~ -dmesgを確認すると selinux で権限が不足らしきメッセージが表示される。~

何らかのモジュールを update した時に、selinux の設定が enforce になっていたのが原因。/etc/sysconfig/selinux を確認。

Windows に建てた HTTP サーバからネットワークインストールすると、パッケージの読み込みエラーが頻発する。

DVDドライブのない PC で、ネットワークインストール用 CD を使って起動し、インストール元として Windows に建てた HTTP サーバを指定すると、パッケージ(RPM)の読み込みエラーが頻発する、という現象。

Linux に建てた HTTP サーバを指定した場合は発生しないことから、Windows 固有の現象と思われます。 アンチウィルスが邪魔をしているのか?、HTTP サーバを建てている Windows マシンでブラウザを開いて、パッケージ(RPM)をダウンロードしようとした場合も、ダウンロード開始までに 15秒程度時間がかかる。

:解決策|Linux マシンに HTTP サーバを建て、DVD をマウントし、そのサーバをインストール元として指定する。

起動時に「/dev/hda3 swapon: /dev/hda3: Invalid argument.」というメッセージが表示される

スワップフアイルが初期化されていない可能性がありますので、初期化しましょう。

mkswap /dev/hda3
du と df の数字が一致しない

du で確認したディレクトリ使用量の合計よりも、df で確認したディスク使用量の方がかなり大きい。

次のコマンドで削除したにもかかわらずプロセスが終了していないため、つかんだままになっているファイルが存在しないか確認します。

lsof | grep deleted



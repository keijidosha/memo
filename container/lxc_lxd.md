- Table of Content  
{:toc}

# LXC／LXD

## images.linuxcontainers.org が閉鎖されたことへの対応

Ubuntu 22.04 で発生。  
Ubuntu 24.04 では次の対応をしなくても最初から Canonical のリモートサーバーが追加されている模様。

* リモートサーバーのリストに Canonical を追加  
  `lxc remote add canonical https://images.lxd.canonical.com --protocol=simplestreams`
* リモートサーバーのリストに Canonical が追加されていることを確認  
  `lxc remote list`
* Canonical のイメージを表示・確認  
  `lxc image list canonical:`
* Canonical から RockyLinux 8 コンテナをインストール  
  `lxc launch canonical:rockylinux/8/amd64 rocky8`
* ダウンロードしたイメージに alias を付与  
  `lxc image alias create <alias> <fingerprint>`  
  alias を付与しておくと、ダウンロード済みのイメージからコンテナを作成可能になる。  
  ※fingerprint は lxc image list で確認  
  (例)  
  ```
  lxc image alias create canonical-ol8.10 9fe68be23287
  lxc launch canonical-ol8.10 oracle8.10
  ```  
  alias を作成していない場合は、fingerprint を指定して作成するとことも可能  
  ```
  lxc launch 9fe68be23287 oracle8.10 
  ```
  

(参考) https://qiita.com/d-ebi/items/eb8f84f230029ef0e2c5  
(images.linuxcontainers.org 閉鎖の案内) https://discuss.linuxcontainers.org/t/important-notice-for-lxd-users-image-server/18479  

## よく使うコマンド

## 準備

* パッケージを最新に更新  
  ※Vagrant/VirtualBox 環境で upgrade すると、addtional tools が使えなくなってネットワークや共有フォルダで問題が出る可能性があるので、簡単なテストをして確認しておいた方がよさそう。  
  まずは upgrade した環境で小さなコンテナを立ててネットワーク接続を確認するなど(例: curl https://www.xxxx.co.jp/。
  ```
  sudo apt update  
  sudo apt upgrade
  ```
* ログインユーザを lxd グループに追加  
`sudo gpasswd -a $USER lxd`  
確認  
`getent group lxd`

「[LXC 5.0 でコンテナから lxdbr0 を通じてインターネットに出れない](#lxc-50-でコンテナから-lxdbr0-を通じてインターネットに出れない)」も済ませておく。

## 設定

* 初期設定  
`lxd init`
* リモートの一覧を表示  
`lxc remote list`
* リモートで公開しているイメージの一覧を表示  
`lxc image alias list images:`
* ローカルにダウンロードずみのイメージ一覧を表示  
`lxc image list`
* ブリッジインターフェースの情報を表示  
`lxc network show lxdbr0`
* コンテナの詳細情報を表示  
`lxc config show --expanded <コンテナ名>`

## プロファイル
* プロファイルの一覧を表示  
`lxc profile list`
* デフォルトプロファイルの内容を表示  
`lxc profile show default`
* 既存のコンテナにプロファイルを割り当て  
`lxc profile assign <コンテナ名> <プロファイル名>`

## 実行

* コンテナの一覧と状態を表示  
`lxc list`
* コンテナの情報を表示  
`lxc info <コンテナ名>`
* **★CentOS7 のコンテナを作成**  
`lxc launch images:centos/7/amd64 <コンテナ名>`  
* 使用可能なイメージの一覧  
`lxc image list images:`  
または   
https://images.linuxcontainers.org/
* コンテナを開始  
`lxc start <コンテナ名>`
* コンテナを終了  
`lxc stop <コンテナ名>`
* コンテナを削除  
`lxc delete <コンテナ名>`
* コンテナに bash で接続  
`lxc exec <コンテナ名> bash`
* lxc のバージョンを表示  
`lxc --version`
* lxd のバージョンを表示  
`lxd --version`

## スナップショット

* コンテナのスナップショットをとる  
`lxc snapshot <コンテナ名> <スナップショット名>`  
すでに同じ名前のスナップショットが存在する場合、上書きする(削除して作成)  
`lxc snapshot <コンテナ名> <スナップショット名> --reuse`  
* コンテナのスナップショット一覧表示  
`lxc info <コンテナ名>`
* スナップショットの有効期限を設定する
  ```
  export EDITOR=vim
  lxc config edit <コンテナ名>/<スナップショット名>
  ```
* コンテナにスナップショットを復元する  
`lxc restore <コンテナ名> <スナップショット名>`
* スナップショットを削除する  
`lxc delete <コンテナ名>/<スナップショット名>`

## コンテナに対してコマンド実行

* コマンド実行  
  * 普通に実行する場合  
  `lxc exec <コンテナ名> bash`
  * ハイフンで始まるオプションなどを指定する場合  
  -\- を指定する。指定しないと lxc コマンドに対するオプションと判断される。  
  `lxc exec <コンテナ名> -- ls -l`
  * コンテナ上でパイプを使う場合  
  bash -c などを使う  
  `lxc exec <コンテナ名> -- bash -c "lsof | grep deleted"`  
  `lxc exec <コンテナ名> -- sh -c "lsof | vi -R -"`  

## ホストディレクトリをコンテナにマウント

* **ホストのディレクトリをコンテナにマウント**  
`lxc config device add <コンテナ名> <デバイス名> disk source=<ホスト側マウント元ディレクトリ> path=<コンテナディレクトリ>`  
(例)  
`lxc config device add linuxcontainer vagrant disk source=/vagrant/ path=/vagrant/`
* マウント解除する場合  
  `lxc config device remove <コンテナ名> <デバイス名>`  

## コンテナのコピー  
lxc 3.x  
`lxc copy --instance-only <コピー元コンテナ> <コピー先コンテナ>`  
※--instance-only: スナップショットはコピーしない  
lxc 2.x  
`lxc copy --container-only <コピー元コンテナ> <コピー先コンテナ>`

## コンテナ名変更
`lxc rename <変更前> <変更後>`  
rename はスナップショット名の変更にも使用可能

## ファイルアップロード/ダウンロード
### アップロード
* ホストからコンテナにファイルコピー  
`lxc file push /localdir/hoge.txt <コンテナ名>/remotedir/`
* ホストからコンテナにディレクトリコピー  
`lxc file push -r /localdir <コンテナ名>/remotedir/`

### ダウンロード
* コンテナからホストにファイルコピー  
`lxc file pull <コンテナ名>/remotedir/hoge.txt /localdir/`
* コンテナからホストにディレクトリコピー  
`lxc file push -r <コンテナ名>/remotedir /localdir/`

## コンテナを別の LXD にコピー(エクスポート・インポート)
### エクスポート元
1. スナップショットを作成  
`lxc snapshot hoge`
1. スナップショット一覧に表示されている、作成されたスナップショットの名前を確認  
`lxc info --verbose hoge`
1. スナップショットに名前を付ける  
`lxc publish hoge/snap0 --alias hoge_exp`
1. スナップショットをイメージとしてエクスポート  
`lxc image export hoge_exp`  
TARBALL が作成される

* 後片付け
  1. publish したイメージの削除  
     lxc image delete hoge_exp
  1. スナップショットの削除  
     lxc delete hoge/snap0

* ワンライナー
  ```
  for C in cntname; do echo ${C}; lxc snapshot ${C} exp; lxc info --verbose ${C}; lxc publish ${C}/exp --alias ${C}_exp; lxc image export ${C}_exp ${C}; lxc image delete ${C}_exp; lxc delete ${C}/exp; done
  ```  
  「exp」という名前のスナップショットを作成してエクスポート

### インポート先
1. lxc image import xxx.tar.gz \-\-alias hoge_exp
1. lxc init [-p lanprofile] hoge_exp hoge

* 後片付け
  1. lxc image delete hoge_exp

* ワンライナー
  ```
  for C in cntname; do echo ${C}; lxc image import ${C}.tar.gz --alias ${C}_exp; lxc init ${C}_exp ${C}; lxc image delete ${C}_exp; done
  ```

## ネットワークデバイス

* ネットワークデバイスの一覧を表示  
lxc network list
* ネットワークデバイスの IPアドレスを設定  
lxc network set lxdbr0 ipv4.address 192.168.100.1/24
* ネットワークデバイスに割り当てる DHCP の範囲を設定  
lxc network set lxdbr0 ipv4.dhcp.ranges 192.168.100.10-192.168.100.99
* ネットワークデバイスの詳細を表示(使用しているコンテナの一覧など)  
lxc network show lxdbr0
* コンテナにネットワークデバイスを追加割り当て  
lxc network attach lxdbr0 <コンテナ名> eth1
* コンテナに割り当てるIPアドレスを設定  
コンテナ自体は DHCP で IP アドレスを要求  
それに対して、設定された IPアドレスを返す  
lxc config device set <コンテナ名> eth1 ipv4.address 192.168.100.200  
(コンテナに割り当てられたIPを表示)  
lxc config device show <コンテナ名>
* コンテナの NIC に割り当てられた MACアドレスを表示  
lxc config get <コンテナ名> volatile.eth0.hwaddr
* コンテナの NIC に MACアドレスを設定  
lxc config set <コンテナ名> volatile.eth0.hwaddr 00:11:22:33:44:55
* コンテナからネットワークデバイスを削除  
lxc config device remove <コンテナ名> eth0 nic name=eth0
* ネットワークデバイスの DHCPアドレスリース一覧を表示  
lxc network list-leases lxdbr0
* ネットワークデバイスを追加したら、そのデバイスに対して DHCP を有効にする  
  ```
  vi /etc/sysconfig/network-scripts/ifcfg-eth1
  DEVICE=eth1
  BOOTPROTO=dhcp
  ONBOOT=yes
  HOSTNAME=mymchine
  NM_CONTROLLED=no
  TYPE=Ethernet
  MTU=
  DHCP_HOSTNAME=`hostname`
  ```
  * eth0 と eth1 の両方で DHCP から IP を取得するように設定していて、eth0 から取得した DNS サーバーに接続したいが、resolv.conf が eth1 から取得した DNS サーバーで上書きされてしまう場合は、ifcfg-eth1 に次の行を追記して、eth1 から DNS 設定を取得しないようにする。
    ```
    PEERDNS=no
    ```

## Tips

* `lxc exec <コンテナ名> bash` で接続した時に読み込まれる設定  
.bash_profile は読み込まれない。  
.bashrc に記述しておく。  
(例)  
  ```
  # vi ~/.bashrc
  export LANG=ja_JP.UTF-8
  ```
  => /etc/bashrc の末尾にでも記述しておくと全ユーザーに反映する。

* vagrant 上の Linux で実行しているコンテナから、ホスト側(例: VirtualBox の共有フォルダ)に書き込めるようにする  
  (例) コンテナの uid 3000, gid 3000 のユーザーを、ホストの uid 1000, gid 1000 にマッピングして、ホスト側のフォルダに書き込めるようにする。  
  (注) idmap は security.privileged が true に設定されていると効かない。  

  * vagrant でフォルダを共有する時に、同じ UID, GID を持つユーザーに合わせておく方法(より確実)。
    1. vagrant のゲストに UID 3000 でユーザーを作成。  
       合わせてコンテナ側でも同じ手順で名前でユーザー hoge を作成しておく。
       ```
       sudo groupadd -g 3000 hoge
       sudo useradd -g hoge -u 3000 -m hoge
       ```
    1. Vagrantfile に共有フォルダの設定を追加
       ```
       config.vm.synced_folder "./share", "/share", owner: "hoge", group: "hoge"
       ```
    1. vagrant でゲストを起動して、コンテナに idmap を 3000 => 3000 で設定
       ```
       lxc config set containername raw.idmap 'both 3000 3000'
       ```
    1. コンテナを起動し、共有フォルダにあるファイルのオーナーが hoge になっていることを確認。
       ```
       lxc start containername
       lxc exec containername bash
       su - hoge
       ls -l /share
       ```
  * vagrant ユーザー(UID: 1000)を、コンテナ上のユーザーの異なる UID にマッピングする方法(この方法だと VirtualBox 7.16 から 7.20 に上げたタイミングで問題が発生)。
    1. コンテナ内で uid 3000 のユーザーを作成する。  
       ```
       groupadd -g 3000 hoge
       useradd -g hoge -u 3000 -m hoge
       ```
    1. コンテナの /etc/subuid と /etc/subgid に次の内容を記述。
       ```
       root:3000:1
       ```  
       ※ 結果的に subuid と subgid は特に設定してなくてもうまく参照できている模様。
    1. ホスト側でコンテナに対して ID のマッピングを設定。  
       ```
       lxc config set <コンテナ名> raw.idmap 'both 1000 3000'
       ```
    1. コンテナを再起動。
       ```
       lxc restart <コンテナ名>
       ```  
       (参考) [LXDコンテナとホストの間でファイルを共有する方法](LXDコンテナとホストの間でファイルを共有する方法)

* Vagrant で共有フォルダを追加し、そのフォルダをコンテナで別アカウントから読み書き可能になるようマッピングする。  
  (例) vagrant 配下のディレクトリを ViutualBox ゲストの uid 1000 のユーザー hoge からコントナの uid 3000 hoge に共有している状態で、配下の share2 を共有して VirtualBox ゲストの /share2 にマウントし、uid 5000 の fuga に読み書き可能になるようマッピング。  
  1. 共有するディレクトリを作成。
     ```
     cd ~/vagrant/vmxxx
     mkdir share2
     ```
  1. Vagrantfile の `Vagrant.configure("2") do |config|` から `end` の間に次の設定を追加して、vagrant(vm) を起動。
     ```
     config.vm.synced_folder "./share2", "/share2", owner: "fuga"
     ```
  1. vm にログインして、VirtualBox ゲストにユーザー fuga を uid 3000 で作成。
     ```
     vagrant ssh
     groupadd -g 3000 fuga
     useradd -g fuga -u 3000 -m fuga -s /bin/bash
     ```
  1. コンテナに入り、uid 5000 でユーザー fuga を作成。
     ```
     lxc start container-name
     lxc exec container-name bash
     groupadd -g 5000 fuga
     useradd -g fuga -u 5000 -m fuga -s /bin/bash
     ```
  1. VirtualBox ゲストで uid 1000 をコンテナの 3000 に、3000 を 5000 にマッピングするようコンテナを設定。  
     => VirtualBox ゲストの uid が +2000 されてコンテナにマッピングされる。
     ```
     lxc config set container-name raw.idmap 'uid 1000-3000 3000-5000'
     ```
  1. コンテナをリスタート
     ```
     lxc restart container-name
     ```

* ホストへのアクセスをコンテナに転送
  * proxy 設定で転送
    ```
    lxc config device add <コンテナ名> <転送設定に付ける名前> proxy listen=tcp:0.0.0.0:8080 connect=tcp:127.0.0.1:80 bind=host
    ```  
    設定確認  
    `lxc config device show <コンテナ名>`  
    設定削除  
    `lxc config device remove <コンテナ名> <転送設定に付けた名前>`
  * iptables で転送  
    ```
    sudo iptables -t nat -A PREROUTING -p tcp -i eth1 --dport 8080 -j DNAT --to-destination 192.168.1.1:80
    ```

### ネットワーク

* 特定の IP アドレスに対して VPN ルーター経由でアクセスする  
  (例) Mac 上の VirtuablBox ゲストにコンテナを設置し、ゲストの仮想 NIC に NAT アダプターとブリッジアダプターの 2種類を割り当てている場合。
  1. ホストOS 側でスタティックルーティングを設定  
     `sudo route add <目的のIP/サブネット> <ルーターIP>`  
     (例) 目的のサブネットが 172.16.1.0/24 で VPN ルーターの IP が 192.168.1.254 の場合  
     `sudo route add 172.16.1.0/24 192.168.1.254`
  1. ゲストOS側では NAT のネットワークアダプター経由で出て行くようスタティックルーティングを設定  
     `sudo ip r add <目的のIP/サブネット> via <NATアダプター側のGWアドレス>`  
     (例) NATアダプター側のゲートウェイ IP が 10.1.2.1 の場合  
     `sudo ip r add 172.16.1.0/24 via 10.1.2.1`  
     ※ブリッジアダプターからパケットが出て行くと、ホストOS のスタティックルーティングが参照されない?(ゲストOS に NAT とブリッジのアダプターが設定されていると、デフォルトではブリッジアダプターからパケットが出て行く。)
     ※ コンテナ側での設定は不要。  
* コンテナに NIC を 2枚割り当てている場合、デフォルトGW が eth1 を向いてしまい、上記の設定をしても eth0 からの VPN を経由しない場合がある。その場合、コンテナ側でも設定を追加。  
  (結論)  
  ※eth0 から抜ける時用の GW IP は lxdbr0 の IP になっているので、直接 lxdbr0 の IP を指定して良さそう。  
  => lxdbr0 の IP はゲストOS で確認。  
  ```
  ip r add 192.168.1.0/24 via <lxdbr0のIP>
  ```
  以下、確認した内容。
  1. eth1 を止める。  
     `sudo ip l set eth1 down`
  1. dhclient のプロセスを停止し、eth0 用の IP を DHCP で取り直して、eth0 用のデフォルトゲートウェイを取り直す。  
     ```
     ps ax | grep dhclient
     sudo kill xxx
     sudo dhclient eth0
     ip r
     ```  
     (例) `default via 172.16.1.1 dev eth0` と表示された場合
  1. もう一度 dhclient のプロセスを停止し、今度は eth0 と eth1 の IP を DHCP で取得
     ```
     ps ax | grep dhclient
     sudo kill xxx
     sudo dhclient eth0 eth1
     ```
  1. 目的のネットワークへのスタティックルーティングを、先ほど取得した GW の IP を経由するように設定
     ```
     ip r add 192.168.1.0/24 via 172.16.1.1 
     ```

## CentOS 7.5 のイメージを作成

今回は root で実行

* tmp ディレクトリに yum 設定ファイルを作成  
cd /tmp  
vi centos75.conf  
  ```
  [main]
  keepcache=0
  exactarch=1
  obsoletes=1
  gpgcheck=0
  plugins=1
  
  [base]
  name=CentOS-7.5 - Base
  baseurl=http://ftp.jaist.ac.jp/pub/Linux/CentOS-vault/7.5.1804/os/x86_64/
  
  [update]
  name=CentOS-7.5 - Update
  baseurl=http://ftp.jaist.ac.jp/pub/Linux/CentOS-vault/7.5.1804/updates/x86_64/
  ```
* rootfs ディレクトリを作成  
mkdir -p /tmp/centos75/rootfs
* base パッケージから Core をインストール  
yum -c /tmp/centos75.conf --disablerepo="*" --enablerepo="base" --installroot=/tmp/centos75/rootfs -y groupinstall Core
* LXCイメージファイルの設定ファイルを作成  
(参考) Unix 時間の取得  
`date +%s`  
vi /tmp/centos75/metadata.yaml  
  ```
  {
      "architecture": "x86_64",
      "creation_date": 1556402782,
      "properties": {
          "architecture": "x86_64",
          "description": "Centos 7.5(x86_64)",
          "name": "centos-7.5-x86_64",
          "os": "centos",
          "release": "7.5",
          "variant": "default"
      }
  }
  ```
* 設定ファイル編集  
  ```
  cd /tmp/centos75
  chroot rootfs/
  echo "export LANG=C" >> /root/.bashrc
  echo "export LANG=C" >> /etc/locale.conf
  exit
  ```
* LXCイメージを圧縮してインポート  
  ```
  tar zcvf /tmp/centos75.tgz metadata.yaml rootfs
  lxc image import centos75.tgz --alias centos7.5  
  ```
※alias を付け忘れた場合は、次のようにしてインポート済みのイメージに alias を設定。  
`lxc image alias create centos7.5 <fingerprint>`  
あとは alias を指定してコンテナを作成。  
`lxc launch centos7.5 c75`

* 一時ディレクトリを削除しようとして machines ディレクトリが削除できない場合  
  ```
  rm -rf centos75
  rm: cannot remove 'centos75/rootfs/var/lib/machines': Operation not permitted
  ```
  btrfs コマンドで machines を削除してから、rm コマンドで全体を削除。  
  ```
  btrfs subvolume delete centos75/rootfs/var/lib/machines/
  Delete subvolume (no-commit): '/tmp/centos75/rootfs/var/lib/machines'
  rm -rf centos75/rootfs/var/lib/
  ```

(参考) [LXC 向け base image を作る](https://qiita.com/summer/items/7fb35a3a6bf03dc63729)

## Oracle Linux 8.4 のイメージを作成

root で実行  
次のように docker で実行  
```
docker run -it --rm -v /vagrant:/vagrant --name lxcbuild oraclelinux:8.4 /bin/bash
```

* tmp ディレクトリに yum 設定ファイルを作成  
cd /tmp  
vi ol84.conf  
  ```
  [main]
  keepcache=0
  exactarch=1
  obsoletes=1
  gpgcheck=0
  plugins=1
  
  [ol8_baseos]
  name=Oracle Linux 8 BaseOS Latest ($basearch)
  baseurl=https://yum.oracle.com/repo/OracleLinux/OL8/4/baseos/base/x86_64/
  
  [ol8_appstream]
  name=Oracle Linux 8 Application Stream ($basearch)
  baseurl=https://yum.oracle.com/repo/OracleLinux/OL8/appstream/x86_64/
  ```
* rootfs ディレクトリを作成  
  ```
  mkdir -p /tmp/ol84/rootfs
  ```
* base パッケージから Core をインストール。さらに dhclient もインストール。  
  ```
  yum -c /tmp/ol84.conf --disablerepo="*" --enablerepo="ol8_baseos,ol8_appstream" --installroot=/tmp/ol84/rootfs -y groupinstall Core
  ```
  ```
  yum -c /tmp/ol84.conf --disablerepo="*" --enablerepo="ol8_baseos,ol8_appstream" --installroot=/tmp/ol84/rootfs -y install dhclient
  ```
* 後で Docker 側で tar 圧縮する時、tar コマンドが必要になるので Docker 環境にインストール(Oracle Linux にはデフォルトで tar がイントールされていない)  
  ```
  yum -c /tmp/ol84.conf --disablerepo="*" --enablerepo="ol8_baseos,ol8_appstream" -y install tar
  ```
* LXCイメージファイルの設定ファイルを作成  
(参考) Unix 時間の取得  
`date +%s`  
vi /tmp/ol84/metadata.yaml  
  ```
  {
      "architecture": "x86_64",
      "creation_date": 1619875931,
      "properties": {
          "architecture": "x86_64",
          "description": "Oralce Linux 8.4(x86_64)",
          "name": "oraclelinux-8.4-x86_64",
          "os": "oraclelinux",
          "release": "8.4",
          "variant": "default"
      }
  }
  ```
* 設定ファイル編集  
`cd /tmp/ol84`  
`chroot rootfs/`  
  ```
  echo "export LANG=C" >> /root/.bashrc
  ```
  ```
  echo "export LANG=C" >> /etc/locale.conf
  ```
  `vi /etc/rc.local`  
  ```
  #!/bin/bash
  
  touch /var/lock/subsys/local
  ```
  `chmod 755 /etc/rc.local`  
  `exit`  
* LXCイメージを圧縮してインポート  
Docker コンテナ内で実行
  ```
  tar zcvf /vagrant/lxc/ol84.tgz metadata.yaml rootfs
  ```
  ホスト側で実行
  ```
  lxc image import /vagrant/lxc/ol84.tgz --alias oraclelinux8.4
  ```

* 作成したイメージを使ってコンテナを作成  
  ```
  lxc launch oraclelinux8.4 ol84
  ```
(注意)  
eth0, eth1(lxc network attach eth0 <コンテナ名> eth1 で追加後) に DHCP アドレスを割り当てがうまくいかない現象の対応  

1. ホスト側のプロミスキャスなNIC を コンテナに割り当て  
lxc network attach <ホスト側のプロミスキャスなNIC> <コンテナ名> <コンテナに割り当てるNIC>  
(例)  
`lxc network attach eth1 <コンテナ名> eth1`
1. /etc/sysconfig/network-scripts/ifcfg-eth0 と ifcfg-eth1 を作成  
  (例)  
    ```
    DEVICE=eth0
    BOOTPROTO=dhcp
    ONBOOT=yes
    HOSTNAME=`cat /proc/sys/kernel/hostname`
    TYPE=Ethernet
    MTU=
    DHCP_HOSTNAME=`cat /proc/sys/kernel/hostname`
    ```
1. vi /etc/rc.local  
次の行を追記  
`dhclient eth0 eth1`  
   * DHCP で DNS 設定を取得する前に nginx が起動するなどして、設定ファイルに指定したホスト名が解決できないような場合  
     /usr/lib/systemd/system/nginx.service の After に rc-local.service を追記して、rc.local を実行してから nginx が起動するようにする。  
     (例) `After=network.target remote-fs.target nss-lookup.target rc-local.service`
1. 実行属性を追加  
`chmod 755 /etc/rc.local`  
実体が別にあれば実体の方を変更(シンボリックリンクされている場合)  
`chmod 755 /etc/rc.d/rc.local`  
1. コンテナ再起動  
`lxc restart <コンテナ名>`  

⬇︎ この方法はうまくいかなかった
eth0 に DHCP アドレスを割り当てるため、Oracle Linux 8.4 のコンテナ起動後、次のコマンド実行が必要?  
`nmcli c modify eth0 ipv4.method auto`

それでもだめな場合は、起動時にコマンド実行して固定IP を割り当てる。  
* /etc/rc.d/rc.local に次の行を追記  
  ```
  ip a add 192.168.1.1/24 dev eth0
  ip r add default via 192.168.1.254
  ```
* /etc/rc.d/rc.local に実行属性を追加  
  ```
  chmod 755 /etc/rc.d/rc.local
  ```
* /etc/resolv.conf に次の行を追記  
  ```
  nameserver 192.168.1.254
  ```

## トラブルシューティング

### ストレージプール(ここでは default プール)の拡張

#### [LXC 5.0]  
##### zfs

```
# zfspool がインストールされていない場合
sudo apt install zfsutils-linux

# プールの使用状況を確認
lxc storage info default
info:
(略)
  space used: 5.41GiB
  total space: 5.59GiB

# ストレージプールを 10GB 拡張
sudo truncate -s +10G /var/snap/lxd/common/lxd/disks/default.img
sudo zpool set autoexpand=on default
sudo zpool online -e default /var/snap/lxd/common/lxd/disks/default.img
sudo zpool set autoexpand=off default
```

##### btrfs

```
# btrfs の使用状況を確認
$ lxc storage info default
info:
(略)
  space used: 5.41GiB
  total space: 5.59GiB

# btrfsイメージファイルのパスとデバイス名を確認
$ losetup
NAME       SIZELIMIT OFFSET AUTOCLEAR RO BACK-FILE                                  DIO LOG-SEC
(略)
/dev/loop4         0      0         1  0 /var/snap/lxd/common/lxd/disks/default.img   1     512

$ sudo truncate -s +10G /var/snap/lxd/common/lxd/disks/default.img
$ sudo losetup -c /dev/loop4
$ sudo mount /dev/loop4 /mnt
$ sudo btrfs filesystem resize max /mnt
Resize device id 1 (/dev/loop4) from 5.59GiB to max
$ sudo btrfs filesystem show
Label: 'default'  uuid: 20355e24-9d32-4424-ab34-1ac359613a9a
  Total devices 1 FS bytes used 5.24GiB
  devid    1 size 15.59GiB used 6.59GiB path /dev/loop4
$ lxc storage info default
info:
  space used: 5.41GiB
  total space: 15.59GiB
```

#### [LXC 3.0]

```
$ df -h
(略)
/dev/loop2     34G   33G   24M 100% /var/lib/lxd/storage-pools/default

$ sudo truncate -s +15G /var/lib/lxd/disks/default.img
$ sudo losetup -c /dev/loop2
$ sudo btrfs filesystem resize max /var/lib/lxd/storage-pools/default/

$ df -h
(略)
/dev/loop2     49G   33G   16G  69% /var/lib/lxd/storage-pools/default
```

### publish
* publish しようとすると「Error: Failed to copy file content: write /var/lib/lxd/images/lxd_build_xxx: no space left on device」というメッセージが出力される  
原因: /var/lib/lxd/images(があるバーティション)の残り容量が少ない  
解決策: /var/lib/lxd/images を余裕のある別のパーティションに移動し、シンボリックリンクをはる  
(例)  
  ```
  sudo mv /var/lib/lxd/images /data/
  sudo ln -s /data/images /var/lib/lxd/
  ```

### vi で日本語が文字化けする
```
vi ~/.vimrc
:set encoding=utf-8
:set fileencodings=iso-2022-jp,euc-jp,sjis,utf-8
:set fileformats=unix,dos,mac
```

### Vagrant でインストールした Ubuntu で lxc のコマンド補完が効かない

[LXC 5.0]

```
sudo ln -s /snap/lxd/current/etc/bash_completion.d/snap.lxd.lxc /etc/bash_completion.d/lxc
```

(参考) [[LXD 4.7] Autocompletion on ubuntu 20.04](https://discuss.linuxcontainers.org/t/lxd-4-7-autocompletion-on-ubuntu-20-04/9480)

[LXC 3.0]

```
sudo  cp -p /usr/share/bash-completion/completions/lxc /etc/bash_completion.d/
```
この状態で `. /etc/bash_completion.d/lxc` すると「-bash: _have: command not found」というエラーが発生する。  
`sudo apt install bash-completion` するのが正しそう  

~~そこで sudo vi /etc/bash_completion.d/lxc して、先頭に次の行を追加して回避。~~
```
_have()
{
    # Completions for system administrator commands are installed as well in
    # case completion is attempted via `sudo command ...'.
    PATH=$PATH:/usr/sbin:/sbin:/usr/local/sbin type $1 &>/dev/null
}
```
(参考) [v1.3.6 installed via Homebrew on macOS causes Bash `_have` error #500](https://github.com/Backblaze/B2_Command_Line_Tool/issues/500#issuecomment-450400179)

### LXC 5.0 でコンテナから lxdbr0 を通じてインターネットに出れない

Ubuntu 22.04 では、フォワードチェーンがデフォルトで無効になっているため、有効にする。

* sudo vi /etc/networkd-dispatcher/routable.d/99-iptables
  ```
  #!/bin/sh
  
  /usr/sbin/iptables -P FORWARD ACCEPT
  ```
* sudo chmod 755 /etc/networkd-dispatcher/routable.d/99-iptables

(参考)  
* [Linux Mint21にしたらLXDのネットワークが外部に繋がらなくなった話](https://zenn.dev/tantan_tanuki/articles/9a68acd97c58d8)
* [No internet connection using default bridge lxdbr0](https://discuss.linuxcontainers.org/t/no-internet-connection-using-default-bridge-lxdbr0/14026)

次のコマンドで確認すると、nftables でパケットがドロップされているという話。  
```bash
sudo nft list ruleset
```

(補足)  
Ubuntu 20.04 あたりから /etc/network/if-up.d/ 配下のスクリプトが実行されなくなっている模様。  
代わりに /etc/networkd-dispatcher/routable.d/ 配下にスクリプトファイルを置く。  
(参考) [Ubuntu Server 20.04 で VXLAN を構成する](https://zenn.dev/microsoft/articles/ubuntu-2004-vxlan?redirected=1)

### LXC 5.0 で CentOS7 のコンテナを実行すると「The image used by this instance requires a CGroupV1 host system」や「Failed to get D-Bus connection」といったエラーが発生する  

* 新規で CentOS7 のコンテナを作成した場合: The image used by this instance requires a CGroupV1 host system  
* LXC 3.0 で作成した CentOS7 のコンテナを LXC 5.0 に移行した場合: Failed to get D-Bus connection

LXC 5.0 ではデフォルトで CGroupV2 が使われるが、この現象を回避するためには(モダンなホストで古いコンテナを動かす場合は)CGroupV1 を使う。  
具体的にはカーネルブートパラメーターに「systemd.unified_cgroup_hierarchy=false」を追加。

1. /etc/default/grub に次のように指定  
`GRUB_CMDLINE_LINUX_DEFAULT="systemd.unified_cgroup_hierarchy=false"`
1. grub を更新  
`sudo update-grub`
1. OS 再起動
1. 次のコマンドを実行して、カーネルブートパラメーターが追加されているか確認  
`cat /proc/cmdline`  
(実行例)  
`BOOT_IMAGE=/vmlinuz-5.15.0-48-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro net.ifnames=0 biosdevname=0 systemd.unified_cgroup_hierarchy=false`

(参考) [Error: The image used by this instance requires a CGroupV1 host system when using clustering](https://discuss.linuxcontainers.org/t/error-the-image-used-by-this-instance-requires-a-cgroupv1-host-system-when-using-clustering/13885)

### LXC 5.0 で nice level を変更するプログラムを実行すると「Failed to set up process scheduling priority (nice level): Permission denied」というエラーが発生

* コンテナに特権を追加  
`lxc config set <コンテナ名> security.privileged true`  
コンテナのリスタートで反映  
確認  
`lxc config show <コンテナ名>`

### Vagrantfile に sync_foldef で共有設定をすると、共有フォルダーのマウントに失敗する(LXC 5.21.2)。

(例) Vagrantfile に次の設定を追加すると

```
config.vm.synced_folder "./share", "/share", owner: "hoge", group: "hoge"
```

次のメッセージが表示される。

> Vagrant was unable to mount VirtualBox shared folders. This is usually because the filesystem "vboxsf" is not available. This filesystem is made available via the VirtualBox Guest Additions and kernel module.

確認1: mount.vboxsf のシンボリックリンクが正しいか確認。

```
ls -l /usr/sbin/mount.vboxsf
```

解決: vagrant-vbguest をインストール

```
vagrant plugin install vagrant-vbguest
```

> Installing the 'vagrant-vbguest' plugin. This can take a few minutes...  
> Fetching micromachine-3.0.0.gem  
> Fetching vagrant-vbguest-0.32.0.gem  
> Installed the plugin 'vagrant-vbguest (0.32.0)'!  

### vagrant 起動時に GuestAdditions のバージョン不一致のメッセージが表示される。

次のメッセージが表示される。

> Got different reports about installed GuestAdditions version:  
> Virtualbox on your host claims:   6.0.0  
> VBoxService inside the vm claims: 7.0.20  
> Going on, assuming VBoxService is correct...  
> [default] GuestAdditions 7.0.20 running --- OK.  

(解決策) Vagrantfile に次の設定を追加。

```
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end
```

(参考) stackoverflow: [vagrant up: Got different reports about installed GuestAdditions version](https://stackoverflow.com/questions/43733108/vagrant-up-got-different-reports-about-installed-guestadditions-version)


## よく使うパターン

```
lxc launch images:centos/7/amd64 <コンテナ名>
lxc stop <コンテナ名>
lxc network attach lxdbr0 <コンテナ名> eth0

# DHCPで割り当てられるIPアドレスが変わらないようにする
lxc config device set <コンテナ名> eth0 ipv4.address 192.168.xxx.xxx

# 外部からの接続が必要な場合だけ NIC を追加
------------------------------------------------------------
lxc network attach eth1 <コンテナ名> eth1
------------------------------------------------------------

# 特定の MACアドレスを割り当てる場合
lxc config set <コンテナ名> volatile.eth0.hwaddr 12:34:56:78:90:ab

# 共有フォルダの uid/gid を合わせる
lxc config set <コンテナ名> raw.idmap 'both 3000 3000'

lxc config device add <コンテナ名> vagrant disk source=/vagrant path=/vagrant
lxc start <コンテナ名>
lxc exec <コンテナ名> bash
timedatectl set-timezone Asia/Tokyo
localectl set-locale LANG=ja_JP.utf8

# 外部からの接続が必要な場合だけ NIC を設定
cp -p /etc/sysconfig/network-scripts/ifcfg-eth{0,1}

# Wi-Fi 接続時など、eth1 経由で外に出ていけない場合は、eth0 をデフォルトゲートウェイに設定する。
vi /etc/sysconfig/network
------------------------------------------------------------
# eth0 側のデフォルトゲートウェイを指定
GATEWAY=xxx.xxx.xxx.xxx
------------------------------------------------------------

# eth1 の DHCP 設定を作成
vi /etc/sysconfig/network-scripts/ifcfg-eth1
------------------------------------------------------------
DEVICE=eth1
BOOTPROTO=dhcp
ONBOOT=yes
HOSTNAME=<コンテナ名>
NM_CONTROLLED=no
TYPE=Ethernet
MTU=
DHCP_HOSTNAME=<コンテナ名>
# DHCP で resolv.conf を更新させたくない場合は、この設定を有効にする。
#PEERDNS=no
------------------------------------------------------------
Wi-Fi 経由でインターネットに接続する場合は、さらに lxdbr0 のネットワークをデフォルトゲートウェイに設定する
(例) lxdbr0 のネットワークが 172.16.0.0/16 の場合
[とりあえず]
ip r del default
ip r add default via  172.16.0.1
[設定]
/etc/sysconfig/network-scripts/ifcfg-eth0
GATEWAY=172.16.0.1
さらに /etc/resolv.conf に DNS を設定(eth1 経由で DHCP で取得した DNS の IP ではなく)

exit
# DHCP 設定を反映させるためにいったん停止して起動
lxc stop <コンテナ名>
lxc start <コンテナ名>
lxc exec <コンテナ名> bash

# IPアドレス割り当てを確認(eth1 は DHCPアドレス取得に少し時間がかかることがある)
ip a
```

## VirtualBox + Vagrant + Ubuntu 22.04 での環境構築

* vagrant up
* vagrant ssh  
ゲスト環境に入る
* sudo apt update
* sudo apt upgrade
* exit  
カーネルが更新されるのでいったんリブート
* vagrant halt
* VirtualBox で ブリッジアダプターのプロミスキャスモードを「すべて許可」する
* vagrant up
* vagrant ssh
* sudo gpasswd -a $USER lxd
* lxd init
* sudo /sbin/ip link set eth1 promisc on
* sudo vi /etc/rc.local  
LAN側から接続できるようにするための設定
  ```
  #!/bin/sh

  /sbin/ip link set eth1 promisc on
  ```
* sudo chmod 755 /etc/rc.local
* sudo vi /etc/networkd-dispatcher/routable.d/99-iptables  
  コンテナからブリッジ(lxdbr0)経由で LAN(インターネット)に出れるようにするための設定
  ```
  #!/bin/sh
  
  /usr/sbin/iptables -P FORWARD ACCEPT
  ```
* chmod 755 /etc/networkd-dispatcher/routable.d/99-iptables
* vi ~/.vimrc  
日本語の文字化け対策
  ```
  :set encoding=utf-8
  :set fileencodings=iso-2022-jp,euc-jp,sjis,utf-8
  :set fileformats=unix,dos,mac
  ```
* sudo apt install bash-completion  
コマンド補完するための設定
* sudo ln -s /snap/lxd/current/etc/bash_completion.d/snap.lxd.lxc /etc/bash_completion.d/lxc

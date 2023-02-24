# LXC／LXD

Table of Contents
=================

   * [Table of Contents](#table-of-contents)
      * [よく使うコマンド](#よく使うコマンド)
      * [準備](#準備)
      * [設定](#設定)
      * [プロファイル](#プロファイル)
      * [実行](#実行)
      * [コンテナのコピー](#コンテナのコピー)
      * [ファイルアップロード/ダウンロード](#ファイルアップロードダウンロード)
         * [アップロード](#アップロード)
         * [ダウンロード](#ダウンロード)
      * [(調査中)LAN から DHCP で IPアドレスを取得し、LAN から接続できるようにする](#調査中lan-から-dhcp-で-ipアドレスを取得しlan-から接続できるようにする)
         * [チャレンジ1](#チャレンジ1)
         * [チャレンジ2](#チャレンジ2)
            * [ホストで実行](#ホストで実行)
         * [★作成済みのコンテナに外部から接続可能な NIC を追加](#作成済みのコンテナに外部から接続可能な-nic-を追加)
      * [ストレージプール](#ストレージプール)
         * [ディスク容量が足りなくなったので、LVM を用意してストレージプールを作成](#ディスク容量が足りなくなったのでlvm-を用意してストレージプールを作成)
            * [ボリュームグループを作成](#ボリュームグループを作成)
            * [ストレージプールを作成してコンテナを作成](#ストレージプールを作成してコンテナを作成)
         * [ストレージプールを LVM で作成](#ストレージプールを-lvm-で作成)
         * [コンテナを別のストレージプールに移動](#コンテナを別のストレージプールに移動)
            * [流れ](#流れ)
            * [手順](#手順)
         * [ボリューム](#ボリューム)
      * [コンテナを別の LXD にコピー(エクスポート・インポート)](#コンテナを別の-lxd-にコピーエクスポートインポート)
         * [エクスポート元](#エクスポート元)
         * [インポート先](#インポート先)
      * [ネットワークデバイス](#ネットワークデバイス)
      * [Tips](#tips)
      * [CentOS 7.5 のイメージを作成](#centos-75-のイメージを作成)
      * [Oracle Linux 8.4 のイメージを作成](#oracle-linux-84-のイメージを作成)
      * [トラブルシューティング](#トラブルシューティング)
         * [btrfs で作成されたストレージプール(ここでは default プール)の拡張](#btrfs-で作成されたストレージプールここでは-default-プールの拡張)
         * [publish](#publish)
         * [vi で日本語が文字化けする](#vi-で日本語が文字化けする)
         * [Vagrant でインストールした Ubuntu で lxc のコマンド補完が効かない](#vagrant-でインストールした-ubuntu-で-lxc-のコマンド補完が効かない)
         * [LXC 5.0 で CentOS7 のコンテナを実行すると「The image used by this instance requires a CGroupV1 host system」や「Failed to get D-Bus connection」といったエラーが発生する](#lxc-50-で-centos7-のコンテナを実行するとthe-image-used-by-this-instance-requires-a-cgroupv1-host-systemやfailed-to-get-d-bus-connectionといったエラーが発生する)
         * [LXC 5.0 で nice level を変更するプログラムを実行すると「Failed to set up process scheduling priority (nice level): Permission denied」というエラーが発生](#lxc-50-で-nice-level-を変更するプログラムを実行するとfailed-to-set-up-process-scheduling-priority-nice-level-permission-deniedというエラーが発生)
      * [よく使うパターン](#よく使うパターン)
      * [VirtualBox   Vagrant   Ubuntu 22.04 での環境構築](#virtualbox--vagrant--ubuntu-2204-での環境構築)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## よく使うコマンド

## 準備

* パッケージを最新に更新  
`sudo apt update`  
`sudo apt upgrade`
* ログインユーザを lxd グループに追加  
`sudo gpasswd -a $USER lxd`  
確認  
`getent group lxd`

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
* コンテナのスナップショットをとる  
`lxc snapshot <コンテナ名> <スナップショット名>`
* コンテナのスナップショット一覧表示  
`lxc info <コンテナ名>`
* コンテナにスナップショットを復元する  
`lxc restore <コンテナ名> <スナップショット名>`
* スナップショットを削除する  
`lxc delete <スナップショット名>`
* lxc のバージョンを表示  
`lxc --version`
* lxd のバージョンを表示  
`lxd --version`
* コマンド実行  
  * 普通に実行する場合  
  `lxc exec <コンテナ名> bash`
  * ハイフンで始まるオプションなどを指定する場合  
  -- を指定する。指定しないと lxc コマンドに対するオプションと判断される。  
  `lxc exec <コンテナ名> -- ls -l`
  * コンテナ上でパイプを使う場合  
  bash -c などを使う  
  `lxc exec <コンテナ名> -- bash -c "lsof | grep deleted"`  
  `lxc exec <コンテナ名> -- sh -c "lsof | vi -R -"`  
* **ホストのディレクトリをコンテナにマウント**  
`lxc config device add <コンテナ名> <デバイス名> disk source=<ホスト側マウント元ディレクトリ> path=<コンテナディレクトリ>`  
(例)  
`lxc config device add linuxcontainer vagrant disk source=/vagrant/ path=/vagrant/`

## コンテナのコピー  
`lxc copy --container-only <コピー元コンテナ> <コピー先コンテナ>`

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

## (調査中)LAN から DHCP で IPアドレスを取得し、LAN から接続できるようにする
### チャレンジ1
> チャレンジ2 で書いているプロミスキャスモードを有効にすることで、有線LAN接続では、この方法でもネットワーク上のホストと接続できることを確認済み
* ホストで実行  
`lxc config device add <コンテナ名> eth1 nic name=eth1 nictype=macvlan parent=enp0s8`
* コンテナ内で実行  
  `vi /etc/sysconfig/network-scripts/ifcfg-eth1`  
    
  ```
  DEVICE=eth1
  BOOTPROTO=dhcp
  ONBOOT=yes
  HOSTNAME=centos7-1
  NM_CONTROLLED=no
  TYPE=Ethernet
  MTU=
  DHCP_HOSTNAME=`hostname`
  ```  
  `systemctl restart network`

### チャレンジ2
> 有線LAN接続であれば、この方法でネットワーク上のホストと接続できることを確認済み
#### ホストで実行  
* default プロファイルを複製  
`lxc profile copy default lanprofile`  
* LANに出て行く NIC を確認(enp0s8)  
`ip route show default 0.0.0.0/0`  
* 複製した lanprofile の設定を変更  
`lxc profile device set lanprofile eth0 nictype macvlan`  
`lxc profile device set lanprofile eth0 parent enp0s8`  
* 設定を確認  
`lxc profile show lanprofile`  
* **LANに出て行く NIC をプロミスキャスモードに設定**  
`sudo ip link set enp0s8 promisc on`  
または  
`sudo ifconfig enp0s8 promisc`  
* コンテナを作成  
`lxc launch -p lanprofile images:centos/7/amd64 centos7-1`
* ネットワーク状況を確認  
`lxc exec centos7-1 ip a s`  
* DHCP で IPアドレスを取得  
`lxc exec centos7-1 dhclient eth0`  
* IPアドレスが取れたか確認  
`lxc exec centos7-1 ip a s`  
* 起動時にプロミスキャスモードに設定する  
`sudo vi /etc/rc.local`  
  
  ```
  #!/bin/sh
  
  /sbin/ip link set enp0s8 promisc on
  ```  
  `sudo chmod 755 /etc/rc.local`

あとはコンテナ起動時に DHCP サーバから IPアドレスを取得する設定をする  
(例)  
```
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
HOSTNAME=mycentos
NM_CONTROLLED=no
TYPE=Ethernet
MTU=
DHCP_HOSTNAME=mycentos
```

### ★作成済みのコンテナに外部から接続可能な NIC を追加

```
lxc network attach <ホスト側のプロミスキャスなNIC> <コンテナ名> <コンテナに割り当てるNIC>
```

(例)  
```
lxc network attach eth0 mycentos eth1
```

## ストレージプール
### ディスク容量が足りなくなったので、LVM を用意してストレージプールを作成
#### ボリュームグループを作成
* パーティションを作成  
  `sudo fdisk /dev/sdb`  
  
  ```
  Command (m for help): n
  Select (default p): p
  Partition number (1-4, default 1): 1
  Command (m for help): t
  Selected partition 1
  Hex code (type L to list all codes): 8e
  Command (m for help): w
  ```
* 物理ボリューム作成  
`sudo pvcreate /dev/sdb1`
* 作成した物理ボリュームを確認  
`sudo pvdisplay`
* ボリュームグループを作成  
`sudo vgcreate VGdata /dev/sdb1`
* 作成したボリュームグループを確認  
`sudo vgdisplay`
* 論理ボリュームを作成  
`sudo lvcreate -n LVdata -l 100%FREE VGdata`
* 作成したボリュームグループを確認  
`sudo lvdisplay`
* ボリュームグループを ext4 で初期化  
`sudo mkfs -t ext4 /dev/mapper/VGdata-LVdata`
* /data ディレクトリを作成してボリュームグループをマウント  
`sudo mkdir /data`  
`sudo mount -t ext4 /dev/mapper/VGdata-LVdata /data`
* 起動時に自動的にマウントされるように設定  
  `sudo vi /etc/fstab`  
  ```
  /dev/mapper/VGdata-LVdata /data ext4 defaults,noatime 0 0
  ```

#### ストレージプールを作成してコンテナを作成
* ストレージプール用ディレクトリを作成(パーティション直下にストレージプールを作成できない)  
`sudo mkdir /data/lxd`  
`lxc storage create pool2 dir source=/data/lxd`
* ストレージプールの一覧を表示  
`lxc storage list`
* ストレーブプールの情報を表示(どのコンテナから使われているかなど)  
`lxc storage info <プール名>`
* 作成したストレージプールを使ってコンテナを作成  
`lxc launch -p lanprofile -s pool2 images:centos/7/amd64 <コンテナ名>`
* プロファイルのデフォルトストレージを変更  
`lxc profile device set lanprofile root pool pool2`

### ストレージプールを LVM で作成

* バーテションを作成  
  ```
  sudo fdisk /dev/sdc
  Command (m for help): n
  Partition type
     p   primary (0 primary, 0 extended, 4 free)
     e   extended (container for logical partitions)
  Select (default p): p
  Partition number (1-4, default 1): 
  First sector (2048-629145599, default 2048): 
  Last sector, +sectors or +size{K,M,G,T,P} (2048-629145599, default 629145599): 
  
  Created a new partition 1 of type 'Linux' and of size 300 GiB.
  Command (m for help): w
  ```
* ブロックデバイスとして作成したパーティションを指定し、ストレージプールを作成  
`lxc storage create lvmpool lvm source=/dev/sdc1`

### コンテナを別のストレージプールに移動
#### 流れ
1. イメージ作成
1. プロファイルとストレージプールを指定して、イメージからコンテナを別の名前で作成
1. 元のコンテナを削除
1. 作成したコンテナをリネーム
1. イメージを削除

#### 手順

* コンテナからイメージを作成  
`lxc publish hoge-container --alias hoge-image`
* イメージ一覧を表示して確認  
`lxc image list`
* プロファイルとストレージプールを指定して、イメージからコンテナを作成  
`lxc init hoge-image -p lanprofile -s lvmpool hoge2-container`
* コンテナの一覧を表示して作成されているか確認  
`lxc list`
* コンテナの情報を表示して確認  
`lxc info hoge2-container`
* 元のコンナテを削除  
`lxc delete hoge-container`
* 作成したコンテナを元のコンテナの名前でリネーム  
`lxc mv hoge2-container hoge-container`
* コンナテ一覧を表示して確認  
`lxc list`
* 作成したイメージを削除  
`lxc image delete hoge-image`
* イメージの一覧を表示して削除されたか確認  
`lxc image list`

### ボリューム

* ボリュームの作成  
`lxc storage volume create <ストレージプール名> <作成するボリューム名>`
* ボリュームの情報を表示  
`lxc storage volume show <ストレージプール名> <表示するボリューム名>`
* ボリュームの一覧を表示  
`lxc storage volume list <表示対象ストレージプール名>`
* ボリュームをコンテナにマウント  
`lxc storage volume attach <ストレージプール名> <ボリューム名> <コンテナ名> <マウントポイント>`  
※コンテナ起動中でもマウント可能
* ボリュームをデタッチ  
`lxc storage volume detach <ストレージプール名> <ボリューム名> <コンテナ名>`

* ★ホストのディレクトリをコンテナと共有  
`lxc config device add <コンテナ名> <共有名> disk source=<ホストのディレクトリ> path=<コンテナのディレクトリ>`  
コンテナが起動中でも、即反映される  
コンテナのディレクトリは作成しておかなくても OK

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
  1.  スナップショットの削除  
lxc delete hoge/snap0

### インポート先
1. lxc image import xxx.tar.gz --alias hoge_exp
1. lxc init [-p lanprofile] hoge_exp hoge

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

* vagrant 上の Linux で実行しているコンテナから、VirtualBox の共有フォルダに書き込めるようにする  
コンテナ内で uid 1000 のユーザーを作成する。  
共有フォルダのオーナーが uid 1000 になっているので。

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

### btrfs で作成されたストレージプール(ここでは default プール)の拡張

[LXC 5.0]
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

[LXC 3.0]
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

(参考) [Linux Mint21にしたらLXDのネットワークが外部に繋がらなくなった話](https://zenn.dev/tantan_tanuki/articles/9a68acd97c58d8)

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
確認  
`lxc config show <コンテナ名>`

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

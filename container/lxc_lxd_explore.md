# LXC／LXD 調査

Table of Contents
=================

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


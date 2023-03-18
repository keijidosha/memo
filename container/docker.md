# Docker

Table of Contents
-----------------

   * [Table of Contents](#table-of-contents)
      * [docker コマンド](#docker-コマンド)
         * [イメージ](#イメージ)
         * [コンテナ](#コンテナ)
         * [マウント・ボリューム](#マウントボリューム)
      * [ビルド](#ビルド)
      * [コンテナの移行](#コンテナの移行)
         * [移行元](#移行元)
         * [移行先](#移行先)
      * [ネットワーク](#ネットワーク)
      * [Docker Compose](#docker-compose)
      * [docker-machine](#docker-machine)
      * [Docker インストール](#docker-インストール)
         * [Ubuntu](#ubuntu)
         * [CentOS](#centos)
         * [インストール後](#インストール後)
         * [docker-compose](#docker-compose-1)
      * [応用](#応用)
      * [Docker で NFSサーバー](#docker-で-nfsサーバー)
      * [各OSのコンテナ起動](#各osのコンテナ起動)
      * [Tips](#tips)
      * [その他](#その他)

## docker コマンド

### イメージ

* Docker イメージをダウンロード  
docker pull <イメージ名>[:タグ名]  
(例)  
`docker pull ubuntu`
* イメージの一覧を表示する  
docker images -a
* タグ名を指定してイメージを一覧表示  
docker images -a \\*:latest
* イメージの ID だけを表示  
docker images -q
* イメージの中身を確認する  
docker images [option] <イメージ名>[:タグ名]
* イメージを削除する  
docker rmi <イメージ名|イメージID>  
* コンテナから参照されていないメイージをすべて削除  
docker image prune
* イメージのタグを設定する  
`docker tag <変更前のイメージ名:タグ名> <変更後のイメージ名:タグ名>`  
※新しいイメージを commit する前に、既存イメージの latest タグを変更しておく  
=> 新しく作成したイメージのタグが latest になり、既存イメージのタグは <none> になってしまうため。
* イメージのレイヤーを確認する  
docker history <イメージ名>

### コンテナ

* イメージからコンテナを作る  
  docker run [option] イメージ名[:タグ] [command] [parameters]  
  (例)  
  `docker run -it ubuntu /bin/bash`  
  -i: 標準入力を使う  
  -t: 標準出力を使う  
   --name=<コンテナ名>: コンテナに名前を付ける(省略した場合は Docker が <形容詞>_<人名> の形式で名前を付ける)  
  * exit すると、コンテナは停止する
  * デタッチする(コンテナを動かしたままコンテナから抜ける)  
    control + P, Control + Q
  * デタッチしたコンテナにアタッチする  
    docker attach <コンテナID>
* コンテナの詳細情報を表示する  
docker inspect <コンテナID>
* ポート転送をしてバックグラウンドで起動する  
(例) Apache HTTPD をバックグラウンドで起動し、ホスト上で 8080 に到着したパケットを転送する  
`docker run -p 8080:80 -d httpd`  
-p: ホストの受信ポート:コンテナへの転送先ポート  
-d: バックグラウンドで起動  
* コンテナ内でシステム時刻を変更できる権限を追加して起動する  
`docker run -it --cap-add=SYS_TIME ubuntu /bin/bash`  
(参考) [Docker privileged オプションについて](https://qiita.com/muddydixon/items/d2982ab0846002bf3ea8)
* **バックグラウンドで実行しているコンテナに接続する**  
docker exec -it (コンテナ名|コンテナID) <コマンド>  
(例) `docker exec -it alpine-container /bin/bash`
* バックグラウンドで起動しているコンテナを停止  
docker stop [option] (コンテナID|コンテナ名)
* 起動中コンテナの一覧を表示  
docker ps
* 停止中のコンテナも含めて一覧表示  
docker ps -a  
-a: 停止中も含めてすべてのコンテナを表示  
-q: コンテナID だけを表示  
-l: 最後に作成したコンナテ情報を表示  
=> (例) `docker start -ai $(docker ps -lq)`
* コンテナのサイズを表示  
docker ps -as  
-s: サイズを表示
* 停止中のコンテナを再開する  
docker start [option] コンテナID  
(例)  
`docker start -ai 1a`  
-a: アタッチ  
-i: 標準入力を使う  
=> コンテナに対話的に接続して再開する  
コンテナID はユニークに識別できる文字数だけ入力すればよい
* コンテナを削除する  
docker rm [option] コンテナID [コンテナID ...]  
コンテナが停止中の時だけコンテナを削除できる  
* 稼働中でないコンテナを一括削除  
docker container prune  
※**ボリューム・コンテナも一緒に消えてしまうので注意が必要**
* コンテナのログを確認  
docker logs <コンテナ名>
* コンテナをイメージ化する  
`docker commit <コンテナID> <イメージ名>`  
コンテナのイメージ化は、コンテナが動作中でも可能  
* イメージのファイル化
  * イメージを save  
    * docker save [option] **イメージ名**[:タグ名]  
    => イメージ名、タグ名は保存される  
    * docker save [option] **イメージID**  
    => イメージ名、タグ名は保存されない  
  * コンテナを export  
    docker export [option] (コンテナ名|コンテナID)  

### マウント・ボリューム

* ホストのディレクトリをマウントする  
docker run **-v <ホストディレクトリ>:<マウント先コンテナディレクトリ>** <イメージ名>
* データボリュームを作成  
docker volume create --name=<ボリューム名>  
(例)  
`docker volume create --name=myvol`
* データボリュームの一覧を表示  
docker volume ls
* ボリュームをマウントしてコンテナを起動  
docker run **-v <ボリューム名>:<マウント先コンテナディレクトリ>** <イメージ名>  
(例)  
`docker run -it -v myvol:/mnt/ busybox /bin/sh`
* ボリュームを削除  
※ボリュームを削除する場合は、事前にボリュームを使っているコンテナを削除しておくこと  
docker myvol rm <ボリューム名>  
* マウントされていないボリュームをまとめて削除  
docker volume prune
* ボリューム・コンテナを作成  
  * ボリュームを作成  
    docker volume create --name=<ボリューム名>  
    (例) `docker volume create --name=vol-for-container`  
  * 名前とマウント先を指定して、ボリューム・コンテナ(ボリュームをマウントするコンテナ)を作成  
    docker run **--name=<コンテナ名> -v <ボリューム名>:<マウント先コンテナディレクトリ>** <イメージ名>  
    (例) `docker run --name=volume-container -v vol-for-container:/mnt busybox`  
  * ボリューム・コンテナをマウントして、通常のコンテナを作成  
    docker run **--volumes-from <ボリュームコンナテ名>** イメージ名  
    (例) `docker run --volumes-from volume-container -it ubuntu /bin/bash`  
* ボリューム・コンテナ作成と同時にボリュームを作成することも可能(事前のボリューム作成なしで)  
(例) 次のコマンドを実行すると、new-vol-for-container というボリュームが自動的に作成され、コンテナにマウントされる。  
`docker run --name=volume-container -v new-vol-for-container:/mnt busybox`  
* ボリューム・コンテナからボリュームのデータをバックアップ  
docker run --volumes-from <ボリューム・コンテナ名> -v <ホストのバックアップディレクトリ>:<バックアップディレクトリのマウント先> <イメージ名> tar cvf <バックアップディレクトリのマウント先>/<バックアップファイル名> -C / <バックアップディレクトリ>  
(例)  
`docker run --volumes-from volume-container -v ~/backup/:/backup/ busybox tar cvf /backup/volume-backup.tar -C / mnt`
* ボリュームのデータをボリューム・コンテナに復元  
docker run --volumes-from <ボリューム・コンテナ名> -v <ホストのバックアップディレクトリ>:<バックアップディレクトリのマウント先> <イメージ名> tar xvf <バックアップディレクトリのマウント先>/<バックアップファイル名> -C /  
(例)  
`docker run --volumes-from volume-container -v ~/backup/:/backup/ busybox tar xvf /backup/volume-backup.tar -C /`
* とりあえずボリューム内のファイルを操作するのに一時的なコンテナを作成してマウントする  
`docker run -it --rm --mount source=<Volume>,target=<MountPoint> alpine sh`

## ビルド

* Dockerfile からイメージをビルド  
docker build -t <イメージ名> .  
-t: '名前:タグ' 形式で名前とオプションのタグを指定

## コンテナの移行

### 移行元

* コンテナをイメージ化  
`docker commit <コンテナID・コンテナ名> <イメージ名>:<タグ>`
* イメージの ID を確認  
`docker images`
* イメージをファイルに出力  
`docker save -o <出力ファイル名>.tar <イメージID>`

### 移行先
* イメージファイルをロード  
`sudo docker load -i <ファイル名>.tar`
* ロードしたイメージの ID を確認  
`sudo docker images`
* イメージ名とタグを割り当て  
`sudo docker image tag <イメージID> <イメージ名>[:タグ]`

## ネットワーク

* ネットワークを作成  
docker network create <ネットワーク名>
* ネットワークの一覧を表示  
docker network ls
* ネットワークを削除  
docker network rm (ネットワーク名|ネットワークID)

## Docker Compose

* すべてのコンテナを起動  
docker-compose up
* すべてのコンテナをバックグラウンドで起動  
docker-compose up -d
* docker-compose.yml に含まれる特定のコンテナだけを起動  
docker-compose run <コンテナ名>
* すべてのコンナテを停止  
docker-compose stop
* コンナテを削除  
docker-compose rm
* コンテナとネットワークを削除  
docker-compose down
* 実行中のコンテナに接続してコマンドを実行  
docker-compose.yml があるディレクトリに移動  
docker-compose exec <コンテナ名> <コマンド>  
(例)  
`docker-compose exec web /bin/bash`


## docker-machine

* 作成  
docker-machine create [--driver virtualbox] <マシン名>
* 起動  
docker-machine start <マシン名>
* 接続  
docker-machine ssh <マシン名>
* 停止  
docker-machine stop <マシン名>
* 強制停止  
docker-machine kill <マシン名>
* 更新  
docker-machine upgrade <マシン名>
* コピー  
docker-machine scp <ローカルファイルパス> <マシン名>:<コピー先のパス>
* 削除  
docker-machine rm <マシン名>

## Docker インストール

### Ubuntu
1. sudo apt update
1. sudo apt upgrade
1. sudo apt install docker.io
1. docker -v
1. sudo usermod -aG docker $USER  
=> 再ログイン後に反映

### CentOS
1. sudo yum remove docker docker-common docker-selinux docker-engine
1. sudo yum install -y yum-utils device-mapper-persistent-data lvm2
1. sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
1. sudo yum makecache fast
1. yum list docker-ce.x86_64 --showduplicates | sort -r  
表示された一覧からインストールするバージョンを選択  
   ```
   docker-ce.x86_64            3:19.03.2-3.el7                     docker-ce-stable
   docker-ce.x86_64            3:19.03.1-3.el7                     docker-ce-stable
   docker-ce.x86_64            3:19.03.0-3.el7                     docker-ce-stable
   (中略)
   docker-ce.x86_64            17.03.2.ce-1.el7.centos             docker-ce-stable
   docker-ce.x86_64            17.03.1.ce-1.el7.centos             docker-ce-stable
   docker-ce.x86_64            17.03.0.ce-1.el7.centos             docker-ce-stable
   ```
1. sudo yum install docker-ce-19.03.2-3.el7
1. sudo systemctl enable docker
1. sudo systemctl start docker
1. docker -v

### インストール後
```
# バージョン確認
docker -v
# セキュリティリスクを認識した上でログインユーザーを docker グループに追加
sudo usermod -aG docker $USER
# いったんログアウントすると反映される
exit
# 再ログイン後に docker コマンドを実行できることを確認
docker images
```

### docker-compose

```
curl -L https://github.com/docker/compose/releases/download/<バージョン>/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

[Release docker/compose](https://github.com/docker/compose/releases/)

## 応用

* CentOS 7.5 のイメージを取得  
docker pull centos:centos7.5.1804
* **ホスト(Vagrant)のディレクトリをマウント**して**一時的な CentOS 7.5 のコンテナを作成**して bash を起動し、終わったら削除する  
`docker run -it --rm -v /vagrant/:/mnt centos:centos7.5.1804 bash`

## Docker で NFSサーバー

vi Dockerfile
```
FROM centos:centos7

LABEL maintainer="Hoge<hoge@xxx.com>"

# Overwrite MOUNTPOINT by using --env at "docker run"
ENV MOUNTPOINT="/tmp"

RUN mkdir -p ${MOUNTPOINT} && chmod 777 ${MOUNTPOINT}

# Install reequired packages
RUN yum install -y nfs-utils

# Place s6-overlay to enable services
ADD https://github.com/just-containers/s6-overlay/releases/download/v1.21.4.0/s6-overlay-amd64.tar.gz /tmp/
RUN tar xzf /tmp/s6-overlay-amd64.tar.gz -C / --exclude="./bin"
RUN tar xzf /tmp/s6-overlay-amd64.tar.gz -C /usr ./bin

RUN mkdir -p /etc/cont-init.d \
    && printf "#!/usr/bin/with-contenv sh\nexportfs -r" >> /etc/cont-init.d/00-config

RUN mkdir -p /etc/services.d/rpcbind \
    && printf "#!/usr/bin/with-contenv sh\nrpcbind -f" >> /etc/services.d/rpcbind/run \
    && chmod 755 /etc/services.d/rpcbind/run \
    && cat /etc/services.d/rpcbind/run

RUN mkdir -p /etc/services.d/mountd \
    && printf "#!/usr/bin/execlineb  -P\nforeground { rpc.nfsd -N 2 -N 3 }\nrpc.mountd -F -N 2 -N 3" >> /etc/services.d/mountd/run \
    && chmod 755 /etc/services.d/mountd/run \
    && cat /etc/services.d/mountd/run

# Expose ports for NFS
EXPOSE 111/udp 111/tcp 2049/udp 2049/tcp

RUN echo "${MOUNTPOINT} *(ro,fsid=0,root_squash,no_subtree_check,insecure)" >> /etc/exports
RUN ls -la /etc/services.d/mountd

# Kick init script of s6-overlay
ENTRYPOINT /init
```

```
docker build -t nfs-server1 .
docker run -it --rm --net host --privileged nfs-server1
```

※docker-machine だとエラーが発生(Unable to access /proc/fs/nfsd)。Ubunt 18.04 では成功。

(参考) [Dockerコンテナ同士のNFSのサーバ・クライアント疎通サンプル](http://otiai10.hatenablog.com/entry/2018/03/22/175016)

## 各OSのコンテナ起動
* CentOS 7.5  
docker run -it \--rm -v /vagrant:/vagrant \--name centos75 centos:centos7.5.1804 /bin/bash
* Oracle Linux 8.4  
docker run -it \--rm -v /vagrant:/vagrant \--name ol8 oraclelinux:8.4 /bin/bash
* RHEL 8.4  
docker run -it \--rm -v /vagrant:/vagrant \--name rh8 registry.access.redhat.com/ubi8/ubi:8.4 /bin/bash
* RHEL 8系最新  
docker run -it \--rm -v /vagrant:/vagrant \--name rh8 registry.access.redhat.com/ubi8/ubi:latest /bin/bash
* Ubuntu 22.04  
  docker run -it \--rm -v /vagrant:/vagrant \--name ub2204 ubuntu:22.04 /bin/bash

## Tips
* タイムゾーンを JST に設定  
~/.bash_profile に次の行を追加  
`export TZ=Asia/Tokyo`

## その他

* Docker で使われているストレージドライバーを確認する  
docker info | grep "Storage Driver"


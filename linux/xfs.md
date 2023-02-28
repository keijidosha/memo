# xfs

* 状況表示  
    ```
    xfs_db -c frag -r /dev/sda2
    ```
* 最適化  
`xfs_fsr /dev/sda2`  
  xfs_fsr 実行後、xfs_db で状況を表示してもあまり改善されていませんでした。 が、再起動してからもう一度 xfs_db を実行すると、「fragmentation factor 0.00%」になっていました。

## CentOS で xfs をサポート  
* ダウンロード  
ftp://ftp.riken.jp/Linux/centos/4/centosplus/i386/RPMS/ から次のファイルをダウンロード。
  * kernel-2.6.9-42.0.10.plus.c4.i686.rpm
  * kernel-module-xfs-2.6.9-42.0.10.plus.c4-0.2-1.i686.rpm
  * kernel-devel-2.6.9-42.0.10.plus.c4.i686.rpm
  * xfsdump-2.2.42-1.c4.i386.rpm
  * xfsprogs-2.8.16-1.c4.i386.rpm
  * xfsprogs-devel-2.8.16-1.c4.i386.rpm
  * dmapi-2.2.5-1.c4.i386.rpm
* インストール  
上記ファイルをインストール。
* フォーマット  
3番目の IDE ドライブの最初のパーティションを xfs で強制的にフォーマット。  
`mkfs -t xfs -f /dev/hdc1`  
パーティションがすでに別の形式でフォーマットされている場合、-f を付けないとエラーになります。
* マウント  
次のコマンドを入力して、マウントできるか確認します。  
`mount -t xfs /dev/hdc1 /opt`
* fstab を編集  
/etc/fstab に次の行を追加して、ブート時に自動的にマウントされるようにします。 
`/dev/hdc1 /opt xfs defaults 1 2`  

## Fedora Core 6 で xfsdump をインストール
Fedora Core 6 の標準パッケージには xfsdump が含まれていません。 そこで yum を使ってインストールする必要があります。 

`yum install xfsdump`

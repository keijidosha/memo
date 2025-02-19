- Table of Content  
{:toc}

# VirtualBox

## ストレージ拡張

1. VirtualBox 管理画面左にあるリストの一番上に表示されている「ツール」のバーガーボタン≡をクリックして「メディア」を選択
1. 拡張したゲストの vdi を選択して画面上にあるツールバーの「プロパティ」ボタンをクリック
1. 画面右下に現在のディスクサイズが表示されているので、新しいサイズを入力して「適用」ボタンをクリック
1. lsblk を実行  
   次のように表示された場合は sda を拡張
   ```
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   10G  0 disk
   └─sda1   8:1    0  5.9G  0 part /
   sr0     11:0    1 1024M  0 rom
1. sda を拡張
   ```
   sudo growpart /dev/sda 1
   ```
1. 再度 lsblk を実行して sda1 が拡張されているか確認
   ```
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   10G  0 disk
   └─sda1   8:1    0   10G  0 part /
                       ^^^ ココ
   sr0     11:0    1 1024M  0 rom
   ```
1. df -hT で / のファイルシステムを確認
   ```
   ファイルシス   タイプ   サイズ  使用  残り 使用% マウント位置
   /dev/sda1      xfs        5.9G  5.1G  779M   88% /
   ```
1. xfs の場合は xfs_growfs を実行してパーティションを拡張
   ```
   sudo xfs_growfs -d /
   ```

## システムクロック

* ゲストの時間を一時的に変更する(Vagrant で確認)  
  1. サービス停止  
     `sudo systemctl stop vboxadd-service`
  2. 時刻変更  
     `sudo date -s '2025/01/01 09:00'`
  3. サービス開始  
     `sudo systemctl start vboxadd-service`  
     ゲストの時刻がホストに合わせられる。
* ゲストの CentOS の時間がずれないようにする
  1. メニュー選択して Addtions CD Image をゲストOS が見えるようにする  
     [Devices] - [Insert Guest Addtions CD Image...]
  1. 必要なパッケージをインストール  
     yum install gcc kernel-devel
  1. Addtions CD Image をマウント  
     ```
     mount -t iso9660 -r /dev/sr0 /mnt
     cd /mnt
     ```
  1. カーネルソースのパスを環境変数 KERN_DIR に設定  
     `export KERN_DIR=/usr/src/kernels/2.6.32-696.3.2.el6.x86_64`
  1. インストーラを実行  
     `sh VBoxLinuxAdditions.run`
  1. 時刻同期するかどうかの閾値を 1秒に設定  
    `/usr/sbin/VBoxService --timesync-set-threshold 1000`
  1. 再起動しても有効になるように設定  
    `/usr/bin/VBoxControl guestproperty set "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold" 1000`
  1. 設定を確認  
     ```
     /usr/bin/VBoxControl guestproperty get "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold"
     Oracle VM VirtualBox Guest Additions Command Line Management Interface Version 5.1.22
     (C) 2008-2017 Oracle Corporation
     All rights reserved.
     
     Value: 1000
     ```
     ※VirtualBox 5.1 で実行
* ホストがスリープしたなどでゲストの時間がずれてしまった場合に、ゲストの時刻をシステムクロックに合わせる
  ゲストで実行
  ```
  sudo systemctl stop vboxadd-service && sudo date -s '2020/01/01 09:00' && sudo systemctl start vboxadd-service
  ```
  VirtualBox のサービスを止めた上で、大幅に時刻をずらしておくと、次にサービス開始した時に、少しずつ時刻を合わせていくのではなく、一気に合わせてくれる。

## ゲストのファイルを別ドライブに移動

* ホストのディスク容量が少なくなったなどで、ゲストのファイルを別ドライブに移動  
  ※Windows はこの方法では移動できないので注意。別ドライブへの再インストールが必要になりそう。
  1. ~/VirtualBox/ 配下の VM のフォルダを別ドライブにコピー。  
     (例) /Volumes/SSD/VirtualBox/ にコピー
  1. VirtualBox の画面からコピーした VM を選択して、メニューから「除去」をクリック。  
     この時「除去のみ」を選択し、「すべてのファイルの削除」は選択しない。
  1. VirtualBox の画面から「追加」を選択して、別ドライブにコピーしたフォルダ配下にある、拡張子 .vbox を選択。
  1. 追加した VM を起動して、起動できることを確認。
  1. コピー元のフォルダを削除。

## 別マシンにコピー

* 別のマシンにゲストOSをディレクトリごとコピーした後、コピー先でゲストを追加できない(ディスクイメージが見つからない)  
  * 設定ファイル *.vbox をエディタで開く
    1. \<AttachedDevice> 〜 \</AttachedDevice> をコメントアウトする
    1. ゲストを追加する
    1. ゲストの設定画面を開き、「ストレージ」で「既存のディスクを追加」を選択して、ファイルを選択する

## Addtions インストール

* VirtualBox に Linux をインストールした後、Addtions をインストール。
  1. ゲスト OS を起動。
  1. [Devices] メニユーから [Insert Guest Addtions CD Image...] をクリック
  1. 次のコマンドを実行
     ```
     mount -rt iso9660 /dev/cdrom /mnt
     cd /mnt
     ./VBoxLinuxAdditions.run
     ```
  * カーネルを変更した場合などは次のコマンドを実行(UEK から RHCK など)。
    ```
    /sbin/rcvboxadd quicksetup all
    ```
    * カーネル変更後の addtions 再構築に必要な RPM をインストールする場合
      ```
      sudo yum install kernel-devel kernel-headers dkms gcc gcc-c++
      ```

## VBoxManage コマンド
* ゲストOSを画面なしで起動  
`VBoxManage startvm "GuestName" --type headless`
* 実行中のゲストOS一覧を表示  
`VBoxManage list runningvms`
* ゲストOSの詳細情報を表示  
`VBoxManage showvminfo "GuestName"`
* ゲストOSの一覧を表示  
`VBoxManage list vms`
* 利用可能な OS の一覧を表示  
`VBoxManage list ostypes`

## Mac関連
* ゲストの MacOS に GuestAddition をインストールした後、高解像度に設定する。  
(例) 1920 x 1200 に設定する場合  
  ```
  VBoxManage setextradata "MacOSMountainLion" "VBoxInternal2/EfiHorizontalResolution" 1920
  VBoxManage setextradata "MacOSMountainLion" "VBoxInternal2/EfiVerticalResolution" 1200
  VBoxManage setextradata "MacOSMountainLion" "VboxInternal2/EfiGraphicsResolution" "1920x1200"
  ```

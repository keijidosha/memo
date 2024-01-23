# VirtualBox

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
* 別のマシンにゲストOSをディレクトリごとコピーした後、コピー先でゲストを追加できない(ディスクイメージが見つからない)  
  * 設定ファイル *.vbox をエディタで開く
    1. \<AttachedDevice> 〜 \</AttachedDevice> をコメントアウトする
    1. ゲストを追加する
    1. ゲストの設定画面を開き、「ストレージ」で「既存のディスクを追加」を選択して、ファイルを選択する

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

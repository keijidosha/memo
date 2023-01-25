# VirtualBox

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
* 別のマシンにゲストOSをディレクトリごとコピーした後、コピー先でゲストを追加できない(ディスクイメージが見つからない)  
  * 設定ファイル *.vbox をエディタで開く
    1. \<AttachedDevice> 〜 \</AttachedDevice> をコメントアウトする
    1. ゲストを追加する
    1. ゲストの設定画面を開き、「ストレージ」で「既存のディスクを追加」を選択して、ファイルを選択する
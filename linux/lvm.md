# LVM

## ボリュームグループをマウント  
* ボリュームグループを検索  
    ```
    lvm vgscan
    Reading all physical volumes. This may take a while… Found volume group “VolGroup00” using metadata type lvm2
    ```
* ボリュームグループを使用可に設定  
    ```
    lvm vgchange -a y 2
    logical volume(s) in volume group “VolGroup00” now active
    ```
* 論理ボリュームをマウント  
    ```
    mount /dev/VolGroup00/LogVol00 /mnt/sda1
    ```

## ボリュームグループの名前を変更
* ボリュームグループを使用不可に設定  
    ```
    lvm vgchange -an <ボリュームグループ名>
    ```
* ボリュームグループをリネーム  
    ```
    lvm vgrename <旧ボリュームグループ名> <新ボリュームグループ名>
    ```
## Vagrant + VirtulBox + Ubuntu 22.04 の ext4 でフォーマットされたパーティションを拡張

* Vagrantfile でディスクサイズ変更(要プラグイン)  
    ```
    config.disksize.size = '128GB'
    ```
* Ubuntu を起動して、パーティションエディタで拡張  
    ```
    sudo parted /dev/sda  
    ```
  * パーティション状況を確認  
    ```
    (parted) p
    Model: ATA VBOX HARDDISK (scsi)
    Disk /dev/sda: 137GB
    Sector size (logical/physical): 512B/512B
    Partition Table: gpt
    Disk Flags:
    
    Number  Start   End     Size    File system  Name  Flags
     1      1049kB  2097kB  1049kB                     bios_grub
     2      2097kB  2150MB  2147MB  ext4
     3      2150MB  68.7GB  66.6GB  <== ここが
    ```
  * /dev/sda3 をいっぱいまで拡張  
    ```
    (parted) resizepart 3 100%
    ```
  * 拡張後のパーティション状況を確認  
    ```
    (parted) p                                                                
    Model: ATA VBOX HARDDISK (scsi)
    Disk /dev/sda: 137GB
    Sector size (logical/physical): 512B/512B
    Partition Table: gpt
    Disk Flags: 
    
    Number  Start   End     Size    File system  Name  Flags
     1      1049kB  2097kB  1049kB                     bios_grub
     2      2097kB  2150MB  2147MB  ext4
     3      2150MB  137GB   135GB  <== こうなった
    ```
* 拡張したパーティションは LVM なので、物理ボリュームを拡張  
  ```
  sudo pvresize /dev/sda3
  ```
* 次に論理ボリュームを拡張  
  ```
  sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
  ```
* 最後に ext4 でフォーマットされたファイルシステムを拡張  
  ```
  sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
  ```

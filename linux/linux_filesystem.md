# Linux: ファイルシステム

vagrant で作成した Ubuntu 22.04 に 100GB 割り当てたものの、31GB しか割り当てられていなかったので、98GB に拡張。

* sda3 のサイズを確認  
`fdisk -l /dev/sda`  
`/dev/sda3  4198400 134215679 130017280  62G Linux filesystem`
* /dev/sda3 を拡張  
`sudo cfdisk /dev/sda`  
* sda3 のサイズが拡張されているか確認  
`fdisk -l /dev/sda`  
`/dev/sda3  4198400 209715166 205516767  98G Linux filesystem`
* pv のサイズを確認  
`sudo pvdisplay`  
`PV Size               <62.00 GiB / not usable 0`
* pv をパーティションサイズいっぱいまで拡張  
`sudo pvresize /dev/sda3`
* pv が拡張されているか確認  
`sudo pvdisplay`  
`PV Size               <98.00 GiB / not usable 16.50 KiB`
* vg のサイズを確認  
`sudo vgdisplay`  
  ```
  Alloc PE / Size       15871 / <62.00 GiB
  Free  PE / Size       9216 / 36.00 GiB
  ```
  新規 pe を vg に追加することはできるが、vg を pe いっぱいまでの拡張はできないようなので、ここでは確認するだけにとどめておく
* lv のサイズを確認  
`sudo lvdisplay`  
`LV Size                <31.00 GiB`
* lv を pv サイズいっぱいまで拡張  
`sudo lvresize -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`
* lv のサイズを確認  
`sudo lvdisplay`  
`LV Size                <98.00 GiB`
* ext4 パーティションサイズを確認  
`df -hT`  
`/dev/mapper/ubuntu--vg-ubuntu--lv ext4     31G   23G  6.3G  79% /`
* ext4 パーティションを lv サイズいっぱいまで拡張  
`sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv`
* ext4 パーティションが拡張されているか確認  
`df -hT`  
`/dev/mapper/ubuntu--vg-ubuntu--lv ext4     97G   23G   70G  25% /`

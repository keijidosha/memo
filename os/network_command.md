# ネットワークコマンド

## IPアドレスの alias 割当
* Mac  
(割当) ifconfig en0 192.168.1.201 netmask 255.255.255.0 alias  
または ifconfig en0 alias 192.168.1.201/24  
(解除) ifconfig en0 192.168.1.201 -alias  
または ifconfig en0 -alias 192.168.1.201  
* Linux  
  * CentOS 6  
(割当) ifconfig eth0:0 192.168.1.201 netmask 255.255.255.0  
(解除) ifconfig eth0:0 down  
  * CentOS 7  
(割当) ip addr add 192.168.1.201/24 dev ens1  
(解除) ip addr del 192.168.1.201/24 dev ens1  
addr は a に省略可  
* Windows  
コントロールパネルから設定

## 起動時に IPアドレスの alias を設定
* CentOS  
vi /etc/sysconfig/network-scripts/ifcfg-eth0:0  
  ```
  DEVICE=eth0:0
  BOOTPROTO=none
  IPADDR=192.168.1.201
  NETMASK=255.255.255.0
  ONBOOT=yes
  ```

## デフォルトゲートウェイ設定
* Linux  
route add -net default gw 192.168.1.254
* Mac  
route add -net default 192.168.1.254  
(削除) route delete -net default 192.168.1.254
* Windows

## ルーティングテーブル追加/削除
* Linux  
(追加) route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.2.254
* Mac  
(追加) route add -net 192.168.2.0/24 192.168.2.254  
(削除) route delete -net 192.168.2.0/24  
* Windows  
(追加) route add 192.168.2.0 mask 255.255.255.0 gw 192.168.2.254  
(削除) route delete 192.168.2.0 mask 255.255.255.0  

## DNSキャッシュをクリア
* Mac  
sudo dscacheutil -flushcache
* Linux  
DNSクライアントではDNS情報をキャッシュしない
* Windows  
ipconfig /flushdns

## Sambaマウント
* Mac  
`mount -t smbfs //<userid>:<password>@<host>/<sharename> <mountpoint>`
* Linxu  
`mount -t cifs -o username=<userid>,password=<password>[,uid=<ownerid>,gid=<groupid>] //<host>/<sharename> <mountpoint>`

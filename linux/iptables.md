# iptables

## コマンド

* 一覧表示  
`iptables -nvL`
* 行番号付きで一覧表示  
`iptables -nvL –line-numbers`
* NAT変換テーブルを表示  
`iptables -t nat -nvL`

## 文法

* 否定  
  ! を付ける
  * ! -i eth0
  * ! -s 1.2.3.0/24
  * ! -p udp
  * ! –dport 80

## セキュリティ

### hashlimit

* SSH 接続(TCP SYN)を 1分間に最大 2回まで、それ以降は 1分間に 1回だけ許可する。  
通過する NIC は eth0, 送信元は 1.2.3.0/24  
iptables -A INPUT -p tcp -m state –syn –state NEW –dport 22 -i eth0 -s 1.2.3.0/24 -m hashlimit –hashlimit-name t_sshd –hashlimit 1/m –hashlimit-burst 2 –hashlimit-mode srcip –hashlimit-htable-expire 60000 -j ACCEPT
* UDP  
  連続した UDP のパケットを防ぐ場合、ESTABLISHED 許可よりも前に hashlimit を定義しておく。  
  UDP のパケットが 1度通過すると、しばらくの間 ESTABLISHED の状態が続くため。  
  ```
  iptables -A INPUT -p udp --dport 53 -m hashlimit --hashlimit-name t_dns --hashlimit 1/m --hashlimit-burst 4 --hashlimit-mode srcip --hashlimit-htable-expire 65000 -j ACCEPT
  iptables -A INPUT -p udp --dport 53 -j LOG --log-prefix '[IPTABLES_UDP_DNS_DROP] : ' --log-level=debug
  iptables -A INPUT -p udp --dport 53 -j DROP
  iptables -A INPUT  -m state --state ESTABLISHED,RELATED  -j ACCEPT
  ```
* 上の処理を名前で定義してみる。
  ```
  iptables -N DNS_LIMIT
  iptables -A DNS_LIMIT -p udp --dport 53 -m hashlimit --hashlimit-name t_dns --hashlimit 1/m --hashlimit-burst 4 --hashlimit-mode srcip --hashlimit-htable-expire 65000 -j ACCEPT
  iptables -A DNS_LIMIT -p udp --dport 53 -j LOG --log-prefix '[IPTABLES_DNS_DROP] : ' --log-level=debug
  iptables -A DNS_LIMIT -p udp --dport 53 -j DROP
  
  # その他の定義
  
  iptables -A INPUT -j DNS_LIMIT
  iptables -A INPUT  -m state --state ESTABLISHED,RELATED  -j ACCEPT
  ```
### recent

* 10秒間に 3回を超える SSH接続があった場合、ログに記録してドロップする。
  ```
  iptables -N SSH_RECENT
  iptables -A SSH_RECENT -p tcp --dport 22 -m state --syn --state NEW -m recent --set --name ssh_attack
  iptables -A SSH_RECENT -p tcp --dport 22 -m state --syn --state NEW -m recent --update --seconds 10 --hitcount 3 --rttl --name ssh_attack -j LOG --log-prefix '[SSH_DROP]'
  iptables -A SSH_RECENT -p tcp --dport 22 -m state --syn --state NEW -m recent --update --seconds 10 --hitcount 3 --rttl --name ssh_attack -j DROP
  iptables -A INPUT -j SSH_RECENT
  ```
* hitcount に 20以上を指定する場合  
sudo vi /etc/modprobe.d/iptables.conf  
(/etc/modprobe.d ディレクトリに作成するファイル名は何でも良い)  
`options ipt_recent ip_pkt_list_tot=100`

### その他

  # その他の定義

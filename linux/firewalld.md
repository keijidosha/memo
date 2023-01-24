# firewalld

## 管理

* firewalld を有効化  
`systemctl enable firewalld`
* firewalld を開始  
`systemctl start firewalld`
* 設定を再読み込み  
`firewall-cmd --reload`
* 許可されたポートの一覧を表示  
`firewall-cmd --list-ports --zone=public`
* サービス名で一覧表示  
`firewall-cmd --zone=public --list-services`
* HTTP を許可  
`firewall-cmd --zone=public --add-service=http --permanent`
* 利用可能なゾーンの一覧  
`firewall-cmd --get-zones`
* 設定の確認  
`firewall-cmd --list-all-zones`
* iptables で設定を確認  
`iptables -nL --line-number`

## 設定

* コマンドラインからポート22 を指定して許可  
`firewall-cmd --add-port=22/tcp --zone=public --permanent`
* サービスファイルを作成して SIP, RTP を許可  
  vi /usr/lib/firewalld/services/sip.xml  
  ```
  <?xml version="1.0" encoding="utf-8"?>
  <service>
    <short>SIP</short>
    <description>Session Initiation Protocol</description>
    <port protocol="udp" port="5060"/>
  </service>
  ```
  vi /usr/lib/firewalld/services/rtp.xml
  ```
  <?xml version="1.0" encoding="utf-8"?>
  <service>
    <short>RTP</short>
    <description>Real Time Protocol</description>
    <port protocol="udp" port="58000-59000"/>
  </service>
  ```
  Asterisk の場合、/etc/asterisk/rtp.conf とポート番号を合わせておく
  ```
  rtpstart=58000
  rtpend=59000
  ```
  firewall-cmd --remove-service=sip --zone=public --permanent  
  firewall-cmd --remove-service=rtp --zone=public --permanent  
  firewall-cmd --reload  

  設定内容は /etc/firewalld/zones/public.xml に保存される

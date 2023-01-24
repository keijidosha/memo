# conntrack

* アドレス変換テーブル一覧表示  
`sudo conntrack -L`
* /proc/net/nf_conntrack 書式でアドレス変換テーブル一覧表示  
`conntrack -L -o extended`
* SNAT のアドレス変換テーブルを表示  
`conntrack -L --src-nat`
* 状態表示  
`sudo conntrack -S`
* 接続状況をタイムスタンプとともにリアルタイム表示  
`conntrack -E -o timestamp`
* アドレス変換テーブルをフラッシュ  
`sudo conntrack -F`
* 特定のアドレス変換テーブルの有効期限を表示  
`sudo conntrack -G -p tcp --src 192.168.1.1 --sport 54321 --dst 11.22.33.44 --dport 80`
* 特定のアドレス変換テーブルを削除  
`sudo conntrack -D -p tcp --src 192.168.1.1 --sport 54321 --dst 11.22.33.44 --dport 80`

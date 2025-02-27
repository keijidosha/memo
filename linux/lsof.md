# lsof

## パラメータ
* ファイル名を指定  
lsof <ファイル名>  
(例) `lsof /var/log/httpd/access.log`
* ディレクトリを指定  
lsof +D <ディレクトリ>  
(例) `lsof +D /var/log/httpd/`
* コマンドで指定  
lsof -c <コマンド>  
(例) `lsof -c java`
* 複数のコマンドを指定  
lsof -c <コマンド> -c <コマンド>  
(例) `lsof -c java -c ruby`
* 指定したコマンド以外を表示  
lsof -c ^<コマンド>  
(例) `lsof ^java`
* ユーザを指定  
lsof -u <ユーザ>  
(例) `lsof -u root`
* プロセスIDを指定  
lsof -p <プロセスID>  
  * プロセスの下にぶら下がっているスレッドも対象とする  
    lsof -p <プロセスID> -K -a  
* ネットワーク情報を表示  
  `lsof -i`
* ポート番号を指定  
lsof -i:<ポート番号>  
(例) `lsof -i:80`
* 複数のポート番号を指定  
(例) `lsof -i:80,8080`
* プロトコルを指定  
lsof -i <プロトコル>  
(例) `lsof -i tcp`
* 複数の検索条件を指定(OR条件)  
(例) `lsof -u root -c java`
* 複数の検索条件を指定(AND条件)  
(例) `lsof -u root -c java -a`
末尾に -a を指定
* 一定間隔で実行  
(例:3秒間隔) `lsof -c java -r3`

(参考) [lsofコマンドで覚えておきたい使い方9個](https://orebibou.com/2016/04/lsof%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%81%A7%E8%A6%9A%E3%81%88%E3%81%A6%E3%81%8A%E3%81%8D%E3%81%9F%E3%81%84%E4%BD%BF%E3%81%84%E6%96%B99%E5%80%8B/)

## 例
* IPv4 でリスンしている TCP ポートの一覧を表示  
`sudo lsof -nP -i4TCP -sTCP:LISTEN`
* IPv4 で接続中の TCP ポートの一覧を表示  
`sudo lsof -nP -i4TCP -sTCP:ESTABLISHED`
* 削除したにもかかわらず、プロセスがつかんだまま解放されていないファイルを表示  
`lsof | grep deleted`

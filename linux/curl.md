# curl

* 受信した内容をファイルに保存  
curl http://hostname/hoge.txt -o hoge.txt
* 受信した内容をリモートと同じ名前でファイルに保存  
curl -O http://hostname/hoge.txt
* POSTパラメータ送信  
curl -X POST http://192.168.1.1/update --data 'hoge=fuga&foo=bar'
* SSL接続時に証明書チェックをしない  
curl -k https://hostname/hoge.html
* WebDAVサーバにアップロード  
curl -X PUT -sS http://192.168.1.1/webdav/hoge.txt --upload-file hoge.txt
* POSTでアップロード  
curl -X POST http://192.168.1.1/upload -F "file=@/tmp/hoge.txt"
* Basic認証  
curl \--basic \--user user:password http://192.168.1.1/index.html  
または  
curl -u user:password http://192.168.1.1/index.html  
* HTTPヘッダをセットする  
curl http://192.168.1.1/ -H "User-Agent: hoge"
* HTTPヘッダーだけを表示  
curl -I -http://192.168.1.1/index.html
* HTTPヘッダーとボディの両方を表示  
curl -i -http://192.168.1.1/index.html
* リダイレクトに追従する  
curl -L -http://192.168.1.1/

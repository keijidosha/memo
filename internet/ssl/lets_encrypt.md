# Let's Encrypt

### Mac に certbot をインストール
`brew install certbot`

### DNS認証で証明書を発行
`sudo certbot certonly --manual -d <FQDN> --preferred-challenges dns`

しばらくすると次のメッセージが表示されるので

```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name
_acme-challenge.<FQDN> with the following value:

<認証ID>

Before continuing, verify the record is deployed.
Press Enter to Continue    
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

表示された _acme-challenge.<FQDN> と、<認証ID> を DNSサーバーに登録

(例)  
`txt _acme-challenge.www xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

DNSサーバーに反映された Enterキーで継続

/etc/letsencrypt/live/<FQDN>/ 以下に、fullchain.pem と privkey.pem が生成されるので、サーバーに配置。

## ECC証明書を発行

* 秘密鍵を作成  
openssl ecparam -name prime256v1 -genkey -out private.key
* CSRを作成  
openssl req -new -sha256 -key private.key -out server.csr
* 証明書発行をリクエスト  
証明書やログなどはカレントディレクトリに出力  
表示された認証iD を DNS に登録  
certbot certonly --manual --preferred-challenge=dns -d "*.hoge.com" --csr server.csr --cert-path server.pem --fullchain-path fullchain.pem --work-dir . --config-dir . --logs-dir .

(参考) [MyDNS.jpのLet's Encrypt証明書を ECDSA対応にしつつ、DNS認証を通す](https://tkx.hatenablog.jp/entry/2018/11/04/235920)

## その他

* スタンドアローンで証明書発行  
=> certbot がポート80 で Listen し、認証用ファイルを用意する  
    ```
    certbot certonly --standalone -d www.hoge.com -m fuga@hoge.com --agree-tos -n
    ```
* HTTPサーバーと別のマシン(Mac)で証明書発行をリクエスト  
=> 実行すると xxx な内容で .well-known/acme-challenge/xxx ファイルを作成して、と表示されるので、DocumentRoot 以下にファイルを作成して Eterキーを押す。すると、該当するファイルがあるか、HTTPサーバーに確認のリクエストが飛ぶ。
    ```
    sudo certbot certonly --manual --domain www.hoge.com
    ```


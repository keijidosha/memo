# Let's encrypt(OLD)


## Let's encrypt から証明書を取得

* Mac で実行
* HTTPサーバは立てずに、DNSを使って認証

* インストール  
`sudo git clone https://github.com/lukas2511/letsencrypt.sh.git`
* フックをコピーして修正  
  ```
  cd letsencrypt.sh
  cp -p docs/examples/hook.sh .
  vim hook.sh
  ```
* echo 〜 read の 2行を追加
  ```
  function deploy_challenge {
      local DOMAIN="${1}" TOKEN_FILENAME="${2}" TOKEN_VALUE="${3}"
  
      echo "Set TXT record of _acme-challenge.$DOMAIN to $TOKEN_VALUE"
      read
  
  }
  ```
* 実行して、DNSサーバに設定するドメイン認証情報を取得
  ```
  ./dehydrated -c -d www.hoge.org --challenge dns-01 -k ./hook.sh
  Processing www.hoge.org
   + Signing domains...
   + Creating new directory /Users/hoge/tmp/letsencrypt.sh/certs/www.hoge.org ...
   + Generating private key...
   + Generating signing request...
   + Requesting challenge for www.hoge.org...
  Set TXT record of _acme-challenge.www.hoge.org to hogex-YRZCGRmZDZsiyZVDW2qISB4f8S78QHX1HOGe
  ```
* 表示されたドメイン認証情報をベースに、次の行を DNSサーバに設定(value-domain の場合)  
`txt _acme-challenge.www hogex-YRZCGRmZDZsiyZVDW2qISB4f8S78QHX1HOGe`
* 別のコンソールで DNSサーバに反映済みかチェック  
`dig -t txt _acme-challenge.www.hoge.org @ns2.value-domain.com`
* 反映済みになったら Enterキーを押して、Let's encrypt サーバに認証をしてもらう
  ```
   + Responding to challenge for www.hoge.org...
   + Challenge is valid!
   + Requesting certificate...
   + Checking certificate...
   + Done!
   + Creating fullchain.pem...
   + Done!
  ```
* SAN証明書を発行する場合は、-d パラメータで複数のドメイン名を指定すれば良いのではないかと。。  
`./letsencrypt.sh -c -d www.hoge.org -d ftp.hoge.org --challenge dns-01 -k ./hook.sh`

(2017.01.16追記)  
以前のバージョンでは dehydrated ではなく letsencrypt.sh が用意されていた模様。使い方は同じ。  
`./letsencrypt.sh -c -d www.hoge.org --challenge dns-01 -k ./hook.sh`

(参考)  
https://www.xmisao.com/2016/04/18/get-free-certification-by-letsencrypt-dns-01-authentication.html


## Let's Encrypt から取得した証明書を Tomcat で使う

* ルート証明書(DST Root CA X3)をダウンロード
* 次の URL を開き、Textarea の内容をコピペして、root.pem という名前で保存  
https://www.identrust.com/certificates/trustid/root-download-x3.html  
この時、先頭行に「-----BEGIN CERTIFICATE-----」、末尾行に「-----END CERTIFICATE-----」を付ける
  ```
  -----BEGIN CERTIFICATE-----
  MIIDSjCCAjKgAwIBAgIQRK+wgNajJ7qJMDmGLvhAazANBgkqhkiG9w0BAQUFADA/
  MSQwIgYDVQQKExtEaWdpdGFsIFNpZ25hdHVyZSBUcnVzdCBDby4xFzAVBgNVBAMT
  DkRTVCBSb290IENBIFgzMB4XDTAwMDkzMDIxMTIxOVoXDTIxMDkzMDE0MDExNVow
  PzEkMCIGA1UEChMbRGlnaXRhbCBTaWduYXR1cmUgVHJ1c3QgQ28uMRcwFQYDVQQD
  Ew5EU1QgUm9vdCBDQSBYMzCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
  AN+v6ZdQCINXtMxiZfaQguzH0yxrMMpb7NnDfcdAwRgUi+DoM3ZJKuM/IUmTrE4O
  rz5Iy2Xu/NMhD2XSKtkyj4zl93ewEnu1lcCJo6m67XMuegwGMoOifooUMM0RoOEq
  OLl5CjH9UL2AZd+3UWODyOKIYepLYYHsUmu5ouJLGiifSKOeDNoJjj4XLh7dIN9b
  xiqKqy69cK3FCxolkHRyxXtqqzTWMIn/5WgTe1QLyNau7Fqckh49ZLOMxt+/yUFw
  7BZy1SbsOFU5Q9D8/RhcQPGX69Wam40dutolucbY38EVAjqr2m7xPi71XAicPNaD
  aeQQmxkqtilX4+U9m5/wAl0CAwEAAaNCMEAwDwYDVR0TAQH/BAUwAwEB/zAOBgNV
  HQ8BAf8EBAMCAQYwHQYDVR0OBBYEFMSnsaR7LHH62+FLkHX/xBVghYkQMA0GCSqG
  SIb3DQEBBQUAA4IBAQCjGiybFwBcqR7uKGY3Or+Dxz9LwwmglSBd49lZRNI+DT69
  ikugdB/OEIKcdBodfpga3csTS7MgROSR6cz8faXbauX+5v3gTt23ADq1cEmv8uXr
  AvHRAosZy5Q6XkjEGB5YGV8eAlrwDPGxrancWYaLbumR9YbK+rlmM6pZW87ipxZz
  R8srzJmwN0jP41ZL9c8PDHIyh8bwRLtTcm1D9SZImlJnt1ir/md2cXjbDaJWFBM5
  JDGFoqgCWjBH4d1QB7wCCZAA62RjYJsWvIjJEubSfZGL+T0yjWW06XyxV3bqxbYo
  Ob8VZRzI9neWagqNdwvYkQsEjgfbKbYK7p2CNTUQ
  -----END CERTIFICATE-----
  ```
  ルート証明書の有効期限は2021年9月30日まで
  ```
  Issuer: O=Digital Signature Trust Co., CN=DST Root CA X3
  Validity
      Not Before: Sep 30 21:12:19 2000 GMT
      Not After : Sep 30 14:01:15 2021 GMT
  Subject: O=Digital Signature Trust Co., CN=DST Root CA X3
  ```

* 作成された証明書とプライベートキーファイルを certs ディレクトリにコピー
  ```
  mkdir certs
  cd certs
  cp -p cert.pem privatekey.pem .
  ```
* 取得した証明書チェーンとルート証明書ファイルを certs/ca ディレクトリにコピーし、CA証明書管理として使えるようにハッシュ値のシンボリックリンクを作成
  ```
  mkdir ca
  cp -p chain.pem root.pem ca/
  c_rehash ca/
  ```
* Java の keystore にインポートできるよう PKCS12形式の証明書を作成  
この時、出力するPKCS12証明書のパスワードを必ず入力する。入力しないと後続の keytool へのインポートで失敗する  
`openssl pkcs12 -export -in cert.pem -inkey privkey.pem -out tomcat.p12 -chain -CApath ca/`
* Java の keystore を作成し、PKCS12証明書をインポート  
(このコマンドを実行する時、keystoreファイルは存在していなくてもよい => 新規作成される)  
`keytool -importkeystore -destkeystore tomcat.keystore -storepass <password of keystore> -srckeystore tomcat.p12 -srcstoretype PKCS12 -srcstorepass <password of pkcs12>`
* keystore の内容を確認  
`keytool -v -list -keystore tomcat.keystore -storepass <password of keystore>`

(参考) https://gist.github.com/uemuraj/31973459bb1b87a7efb0

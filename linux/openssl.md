# OpenSSL

## コマンド

### 証明書の内容を表示

* X509
  ```
  openssl x509 -inform der -in xxx.der -text
  openssl x509 -in xxx.pem -text
  ```
* RSA  
`openssl rsa -in xxx.pem -text`
* CSR  
  ```
  openssl req -inform der -in xxx.der -text
  openssl req -in xxx.pem -text
  ```
* CRL
  ```
  openssl crl -inform der -in xxx.der -text
  openssl crl -in xxx.pem -text
  ```

## Tips

### ファイルを暗号化

* 公開鍵(smime)を使う
  * 暗号化
    `openssl smime -encrypt -aes256 -in original.txt -out encrypted.txt smime.cer`
  * 復号化
    `openssl smime -decrypt -aes256 -in encrypted.txt -out original.txt -inkey smime.key -recip smime.cer`
* 共通鍵方式
  * 暗号化  
    `openssl aes-256-cbc -e -in original.txt -out encrypted.txt`
  * 復号化  
    `openssl aes-256-cbc -d -in encrypted.txt -out original.txt`
* 証明書から公開鍵を取り出す  
`openssl x509 -in cert.pem -pubkey > publickey.pem`
* 公開鍵で復号化する  
`openssl rsautl -verify -in encrypted.dat -pubin -inkey publickey.pem`


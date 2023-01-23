# 証明書

## 証明書の表示

プロビジョニングファイルと証明書ファイルが対になっているかどうかを確認する時に使いました。

* 証明書(*.cer)  
oepnssl x509 -inform der -in xxx.cer -text
* 証明書(*.p12)  
次の 2段階で、いったん pem ファイルに変換してから表示  
openssl pkcs12 -in xxx.p12 -out xxx.pem  
openssl x509 -in xxx.pem -text  
(*.cer から *.p12 への変換は、証明書 *.cer をいったんキーチェーンアクセスにインポートしてから、p12 でエクスポート)  
  
  次のコマンドでも表示されますが、上記の方がより詳細な内容が表示されるようです。  
  openssl pkcs12 -info -in xxx.p12  
* CSR  
openssl req -in xxx.csr -text -noout
* APNS証明書  
\*.cer の証明書と同じ  
oepnssl x509 -inform der -in xxx.cer -text
* プロビジョニングファイル  
前半の plist の部分は cat などで表示可能  
後半の証明書部分は次のコマンドで表示可能。(Apple の証明書が表示され、プロビジョニングについての情報はないのであまり使うことはないかも)  
openssl pkcs7 -inform der -in xxx.mobileprovision -text -print_certs  

## APNS用証明書変換

iOS DevCenter からダウンロードした APNS用証明書を PHP などから扱えるようにするための手順。

1. iOS DevCenter から aps_production.cer をダウンロード。
1. aps_production.cer をダブルクリックしてキーチェーンアクセスにインポート。
1. キーチェーンアクセスから p12 形式で書き出す。
1. pkcs12 形式から pem形式に変換。  
openssl pkcs12 -in aps_production.p12 -out aps_production.pem -nodes
1. aps_production.pem をサーバーにアップロード。

(補足)  
1台の端末にアプリを2つ入れ、デバイストークンをサーバーに送信してみると、同じ内容のデバイストークンが送信されていた。  
そのデバイストークンを使って Appleサーバーに通知をリクエストする時に使用するAPNS証明書でアプリ(Bungle ID)を識別しているのかも知れない。  


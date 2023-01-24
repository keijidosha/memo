# nkf

## URL
* URLエンコード  
  `echo 'エンコード対象文字列' | nkf -WwMQ | tr = %`
* URLデコード  
  `echo デコード対象文字列 | nkf -w --url-input`

## メールヘッダー
* JISエンコード  
  ```bash
  echo "サンプルヘッダー" | nkf -W -M -j
  =?ISO-2022-JP?B?GyRCJTUlcyVXJWslWCVDJUAhPBsoQg==?=
  ```
* JISデコード  
  ```bash
  echo "GyRCJTUlcyVXJWslWCVDJUAhPBsoQg==?=" | nkf -J -mB -w
  サンプルヘッダー
  ```
  ```bash
  echo "=?ISO-2022-JP?B?GyRCJTUlcyVXJWslWCVDJUAhPBsoQg==?=" | nkf -J -m -w
  サンプルヘッダー
  ```
* UTF-8エンコード  
  ```bash
  echo "サンプルヘッダー" | nkf -W -M -w
  =?UTF-8?B?44K144Oz44OX44Or44OY44OD44OA44O8?=
  ```
* UTF-8デコード  
  ```bash
  echo "44K144Oz44OX44Or44OY44OD44OA44O8?=" | nkf -W -mB -w
  サンプルヘッダー
  ```
  ```bash
  echo "=?UTF-8?B?44K144Oz44OX44Or44OY44OD44OA44O8?=" | nkf -W -m -w
  サンプルヘッダー
  ```
(参考) http://hogem.hatenablog.com/entry/20100122/1264169093

## その他
* 半角カタカナを全角に変換  
  `nkf -w hoge.txt > hoge2.txt`

## オプション

* 入力文字コード(省略可)  
  * -W: UTF-8
  * -S: SJIS
  * -E: EUC-JP
* 出力文字コード
  * -w: UTF-8
  * -s: SJIS
  * -e: EUC-JP
  * -j: JIS(ISO-2022-JP)
* 改行コード
  * -Lu: LF
  * -Lw: CF + LF
  * -Lm: CR
* その他
  * --overwrite: ファイルを変換結果で上書き
  * -g: 自動認識した文字コードを表示

# Ruby: 文字列

## 部分文字列

* 変数に文字列が含まれているかをチェック
  ```
  if str.index( "abc" ) != nil then
    # 処理
  end
  ```
* 部分文字列を取り出す  
  * 0桁目から 3桁目まで取り出す  
    substr = "1234567890"[0..3]  
  * 0桁目から 3桁取り出す  
    substr = "1234567890"[0, 3]  
  * 後ろから2桁目から右に2桁取り出す(左には取り出せない)  
    substr = "1234567890"[-2, 2]    # "90"  
* 指定された文字(列)で分割  
"10:30:45".split(':')   # => ["10", "30", "45"]  
"2010/2/28 10:30:45".split(/[\/: ]/)   # => ["2010", "2", "28", "10", "30", "45"]  


## 数値系

* 数値に前ゼロを付けて文字列に変換
  ```
  num = 3
  str1 = sprintf( "%02d", num )
  str2 = "%02d" % num
  ```



## 日付・時刻

* 日付文字列を Time に変換  
dt = Time.parse("2015/03/01")  
dt = Time.parse("2015-03-01")  
* 日付・時刻文字列を Time に変換  
tm = Time.parse("2015/03/01 09:15:30")  
tm = Time.parse("2015-03-01 09:15:30")  
ミリ秒  
tm = Time.parse("2015/03/01 09:15:30.568")  
* 時刻文字列を Time に変換  
tm = Time.parse("09:15:30")  
=> 今日の 9時15分30秒に変換  
* Date を文字列に変換  
Time.now.strftime("%Y/%m/%d %H:%M:%S")  
ミリ秒  
Time.now.strftime("%Y/%m/%d %H:%M:%S.%L")  


## 入力/文字チェック

* Unicodeチェック
  ```
  (Ruby >= 2.2)
  if str.unicode_normalized?
    # OK
  else
    # NG
  end
  ```
* JISチェック(ISO-2022-JP)
  ```
  begin
    str.encode('ISO-2022-JP', invalid: nil)
    # OK
  rescue Encoding::InvalidByteSequenceError, Encoding::UndefinedConversionError => e
    # NG
  end
  ```


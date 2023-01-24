# Ruby: 日付・時刻

## 文字列と変換

* 日付文字列を Time に変換  
require "time"  
dt = Time.parse("2015/03/01")  
dt = Time.parse("2015-03-01")  
* 日付・時刻文字列を Time に変換  
require "time"  
tm = Time.parse("2015/03/01 09:15:30")  
tm = Time.parse("2015-03-01 09:15:30")  
ミリ秒  
tm = Time.parse("2015/03/01 09:15:30.568")  
* 時刻文字列を Time に変換  
require "time"  
tm = Time.parse("09:15:30")  
=> 今日の 9時15分30秒に変換  
* Date を文字列に変換  
Time.now.strftime("%Y/%m/%d %H:%M:%S")  
ミリ秒  
Time.now.strftime("%Y/%m/%d %H:%M:%S.%L")  

## 計算

* 1分後の時刻を求める  
now = Time.now  
now += 60  

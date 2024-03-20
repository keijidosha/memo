- Table of Content  
{:toc}

# date

## 書式指定

* date '+%Y/%m/%d %H:%M:%S'
* フォーマット対象の日時を指定して書式化
  * date '+%Y%m%d%H%M%S' -d "2023/01/01 09:15:30"  
    結果: `20230101091530`
  * date '+%Y%m%d%H%M%S' -d "2023-01-01T09:15:30+09:00"  
    結果: `20230101091530`

### ISO8601 形式

* date --iso8601="seconds"
* date --iso8601="seconds" --utc

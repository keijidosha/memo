- Table of Content  
{:toc}

# date

## 書式指定

* date '+%Y/%m/%d %H:%M:%S'
* ミリ秒まで表示  
  date '+%Y/%m/%d %H:%M:%S.%3N'
* フォーマット対象の日時を指定して書式化
  * date '+%Y%m%d%H%M%S' -d "2023/01/01 09:15:30"  
    結果: `20230101091530`
  * ISO8601 形式で指定  
    date '+%Y%m%d%H%M%S' -d "2023-01-01T09:15:30+09:00"  
    結果: `20230101091530`
  * 指定した時刻から 15秒後  
    date '+%Y%m%d%H%M%S' -d "2023/01/01 09:15:30 **15 seconds**"  
    結果: `20230101091545`
  * ISO8601 で指定した時刻から 15秒後  
    date '+%Y%m%d%H%M%S' -d "2023-01-01T09:15:30+09:00 **15 seconds**"  
    結果: `20230101091545`
  * Unix 時間から年月日時分秒に変換
    ```
    date -d "@<UnixTime>
    ```  
    (例)
    ```
    gdate -d "@1745470560"
    ```

### ISO8601 形式

* date --iso8601="seconds"
* date --iso8601="seconds" --utc

## Mac

* Mac で GNU 版 date コマンドを使う  
  ```
  brew install coreutils
  alias date='gdate'
  ```

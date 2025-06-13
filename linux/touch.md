# touch

* ファイルのタイムスタンプを変更する  
`touch -t MMDDhhmm[[CC]YY][.ss] <ファイル名>`  
(例) hoge.txt のタイムスタンプを 2015/01/02 09:15:00 に設定する  
`touch -t 01020915 hoge.txt`  
  * 年月日時分秒を指定  
    `touch -t 201501020915.00 hoge.txt`


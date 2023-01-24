# touch

* ファイルのタイムスタンプを変更する  
`touch -t MMDDhhmm[[CC]YY][.ss] <ファイル名>`  
(例) hoge.txt のタイムスタンプを 2015/01/01 09:00:00 に設定する  
`touch -t 01010900 hoge.txt`  
  * 明示的に年を指定  
    `touch -t 010109002015 hoge.txt`


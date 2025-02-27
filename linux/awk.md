- Table of Content  
{:toc}


# awk

* 先頭の項目を取り出す
  ```
  ps ax | awk '{print $1;}'
  ```
* ファイルから特定の項目を取り出す  
  HOGE= で始まり FUGA に続くカンマまでの文字列を hoge.txt から取り出す  
  ```
  awk 'match($0, /^HOGE=(FUGA.*),.*$/, a) {print a[1]}' hoge.txt
  ```

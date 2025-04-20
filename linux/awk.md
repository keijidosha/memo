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
* ファイルの先頭から16桁を比較して重複している行について、行全体を結果出力する
  ```
  FN=hoge.txt
  awk '{print substr($0,1,16)}' $FN | sort | uniq -d | \
  while read key; do
    grep "^$key" $FN
  done
  ```
  ワンライナー
  ```
  FN=hoge.txt awk '{print substr($0,1,16)}' $FN | sort | uniq -d | while read key; do   grep "^$key" $FN; done
  ```

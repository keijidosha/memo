# grep

* 複数階層にまたがって検索  
`grep -r <検索文字列> <検索ディレクトリ>`  
(例) `grep -r java .`  
* 複数階層にまたがって特定の拡張子のファイルから検索  
`grep -r –include <パターン> --exclude <パターン> <検索文字列> <検索ディレクトリ>`  
(例)  
`grep -r –include “*.txt” --exclude ".svn" java .`
* grep で見つかった前後の行を表示  
  前 3行を表示
  ```
  grep -B3 hoge hoge.txt
  ```
  後ろ 3行を表示
  ```
  grep -A3 hoge hoge.txt
  ```
  前後 3行を表示
  ```
  grep -C3 hoge hoge.txt
  ```

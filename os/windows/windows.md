# Windows

* batファイルで日付をファイル名にして出力する
  ```
  set today=%date:~0,4%%date:~5,2%%date:~8,2%
  hoge.exe > hoge_%today%.log
  ```
* batファイルで時刻をファイル名にして出力する
  ```
  set time2=%time: =0%
  set now=%time2:~0,2%%time2:~3,2%%time2:~6,2%
  hoge.exe > hoge_%now%.log
  ```


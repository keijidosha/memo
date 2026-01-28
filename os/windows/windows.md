# Windows

## ファイル検索

* *.txt を検索
  ```
  Get-ChildItem -Recurse -Filter "*.txt"
  ```
  または
  ```
  gci -r *.log
  ```
* ファイルだけを検索
  ```
  Get-ChildItem -Recurse -File -Filter "hoge"
  ```
* ディレクトリだけを検索
  ```
  Get-ChildItem -Recurse -Directory -Filter "hoge"
  ```
* 日付範囲を指定して検索
  ```
  Get-ChildItem -Recurse | Where-Object LastWriteTime -gt (Get-Date).AddDays(-7)
  ```




## その他

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

## トラブルシューティング

* Power Shell 7 で vim を開いて矩形選択するのに Ctrl + V を押すと、クリップボードの内容がペーストされてしまう。  
  Ctrl + V のキーバインドを無効にて、Ctrl + Shift + V にペーストを割り当てる。  
  settings.json を編集する場合は次のようにする。  
  ```
  "keybindings": 
  [
      {
          "id": "Terminal.CopyToClipboard",
          "keys": "ctrl+c"
      },
      {
          "id": "Terminal.PasteFromClipboard",
          "keys": "ctrl+shift+v"
      },
      {
          その他の設定 ...
          ...
      }
  ],
  ```


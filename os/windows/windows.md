# Windows

## robocopy

* サブディレクトリまで含めて存在しないか更新されたファイルだけをコピー  
  まず /L を指定して dry run して、表示された結果の Files の行の Extras が 0 になっていることを確認できたら(コピー先で削除対象のファイルがないことを確認してから)、/L をはずして本コピー
  ```
  robocopy d:\hoge\fuga c:\hoge\fuga /E /XO /L
  ```
* サブディレクトリまで含めて存在しないか更新されたファイルだけをコピーし、コピー元にないファイルはコピー先からも削除
  ```
  robocopy d:\hoge\fuga c:\hoge\fuga /E /XO /MIR /L
  ```
* /COPY パラメーターに指定可能な値  
  デフォルト値は DAT
  * D: Data（ファイル内容）
  * A: Attributes（読み取り専用などの属性）
  * T: Timestamps（作成/更新/アクセス日時）
  * S: Security（ACL：アクセス権）
  * O: Owner（所有者）
  * U: aUdit（監査情報）

* robocopy でフォルダ差分比較
  robocopy で dry run して確認。
  ```
  robocopy 9.6 src dest /E /FP /NDL /L
  robocopy 9.6 dest src /E /FP /NDL /L
  ```
  双方向で比較して Copied が 0 であればほぼ差分なし(サイズ、タイムスタンプが同じで中身が違う場合は検出されない)  
  または /MIR を付けて確認
  ```
  robocopy 9.6 src dest /E /FP /NDL /MIR /L
  ```

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
  [設定] - [操作] で「貼り付け」のキー割り当てを Ctrl + Shift + V にする。  
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


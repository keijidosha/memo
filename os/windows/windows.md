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
  robocopy src dest /E /FP /NDL /L
  robocopy dest src /E /FP /NDL /L
  ```
  双方向で比較して Copied が 0 であればほぼ差分なし(サイズ、タイムスタンプが同じで中身が違う場合は検出されない)  
  または /MIR を付けて確認
  ```
  robocopy 9.6 src dest /E /FP /NDL /MIR /L
  ```
* その他のパラメーター
  * /NFL: ファイル名のリストを表示しない
  * /NDL: ディレクトリのリストを表示しない

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

## Windows キーショートカット

| キー | 動作 |
|:-:|---|
| Win + A | クイック設定(ネットワーク/Bluetooth/音量など) |
| Win + B | タスクトレイ |
| Win + C | Copilot |
| Win + D | デスクトップ |
| Win + E | エクスプローラー |
| Win + F | フィードバックHUB |
| Win + G | キャプチャ/音声/パフォーマンス |
| Win + H | 音声認識サービス |
| Win + I | 設定 |
| Win + J | リコール(プレビュー) |
| Win + K | キャスト |
| Win + L | ロック |
| Win + M | 最小化 |
| Win + Shift + M | 最小化したウィンドウの最大化 |
| Win + N | 通知 |
| Win + O | なし |
| Win + P | 画面(マルチディスプレイ?) |
| Win + Q | Click Todo ? |
| Win + R | ファイル名を指定して実行 |
| Win + S | 最近実行したもの ? |
| Win + T | タスクバー |
| Win + U | アクセシビリティ設定 |
| Win + V | クリップボード |
| Win + W | ニュース ? |
| Win + X | 管理メニュー ? |
| Win + Y | なし |
| Win + Z | なし |
| Win + 1～9 | タスクトレイのアプリを前面に表示 |
| Win + ;(+) | 拡大鏡 |
| Win + : | 絵文字 |
| Win + , | デスクフトップを一時的に表示 |
| Win + . | 絵文字 |
| Win + F1 | Windows ヘルプ ? |
| Win + Ctrl + D  | 新しい仮想デスクトップ |
| Win + Ctrl + F4 | 現在の仮想デスクトップを閉じる |
| Win + Home | 他のウィンドウを最小化 |
| Win + PrintScreen | デスクトップのスクリーンショット |
| Win + Pause | システム設定 |



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


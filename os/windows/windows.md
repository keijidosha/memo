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

* 大量の小さなファイルのコピーで動作が不安定になる場合は /MT:1(同時 1スレッドで実行)を試してみる。
  ```
  robocopy d:\hoge\fuga c:\hoge\fuga /E /XO /MT:1 /L
  ```

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
  * /NP: 進捗率の % を表示しない
  * /FFT: FAT/exFAT の2秒の丸目の誤差を無視する
  * /LOG:xxx.log: 画面に表示せずログ出力する

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
* 見つかったファイルを削除: find . -name .DS_Store -delete を PowerShell で(例: .DS_Store)
  ```
  Get-ChildItem -Recurse -Force -Filter ".DS_Store" | Remove-Item -Force
  ```
  カレントディレクトリ以下から削除
  * 直接削除ではなく、ごみ箱に入れる(ワンライナー版)
    ```
    Add-Type -AssemblyName Microsoft.VisualBasic; Get-ChildItem -Recurse -Force -Filter ".DS_Store" | ForEach-Object { [Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile($_.FullName,'OnlyErrorDialogs','SendToRecycleBin') }
    ```

## ファイル内を検索(grep)

* findstr を使う
  ```
  findstr /i search_text c:\xxx\xxx.txt
  ```
  * /i: 大文字・小文字を区別しない
  * /n: 行番号表示
  * /r: 正規表現
  * /s: サブディレクトリも検索
    ```
    findstr /i /s search_text *.log
    ```
* Select-String を使う
  ```
  sls find_text xxx.log
  ```
  * 正規表現
    ```
    sls -Pattern "^hoge" -Path xxx.log
    ```
  * 大文字・小文字を区別
    ```
    sls -CaseSensitive "hoge" -Path xxx.log
    ```
  * サブディレクトリも検索
    ```
    sls "hoge" *.log -Recurse
    ```
    
## その他コマンド

### ファイル系

* tail -f
  ```
  Get-Content -Path hoge.log -Encoding UTF8 -wait -tail 10
  ```
  * -wait: ファイルを閉じずに開き続け、新しい行が追記されるのをリアルタイムで待ち受けます。
  * -tail: ファイルの最初からではなく、最後の 10 行だけを読み取ります。巨大なログファイルを開く際に非常に高速です。
* ディレクトリ作成
  ```
  New-Item -ItemType Directory -Force -Path c:\tmp
  ```
  カンマで複数指定可能
  ```
  New-Item -ItemType Directory -Force -Path c:\tmp,c:\Users\hoge\tmp
  ```

#### リンク

* ハードリンク
  ```
  New-Item -ItemType HardLink -Path link.txt -Target original.txt
  ```
  * -Target: リンク元
  * -Path: リンク先
* シンボリックリンク
  ```
  New-Item -ItemType SymbolicLink -Path link.txt -Target original.txt
  New-Item -ItemType SymbolicLink -Path linkdir -Target originaldir
  ```
* ジャンクション(ディレクトリのみ)
  ```
  New-Item -ItemType Junction -Path linkdir -Target originaldir
  ```


## Windows キーショートカット

| キー | 動作 |
|:-:|---|
| Win + A | クイック設定(ネットワーク/Bluetooth/音量など) |
| Win + B | タスクトレイ |
| Win+ Ctrl + Shift + B | GPU リセット |
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


## WSL2

* WSL2 の制限事項
  * ブリッジネットワークが使えない。基本 NAT のみ。
  * ZFS, btrfs が使えない。
   * ZFS は Hyper-V でも安定しない可能性ありとの情報あり。btrfs は Hyper-V では OK。
  * firewalld が使えない。

## Hyper-V

* Hyper-V では WSL2 のような /mnt/c や VirtualBox のような共有フォルダは使えない。
  SMB を使う。


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
* tail の代わり
  ```
  Get-Content -Path logfile.log -wait -tail 0 -Encoding UTF8
  ```
* 継続行  
  Unix系では「\」(バックスラッシュだが、Windows(PowerShell)では「`」(バッククォート)

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
  この設定によりクリップボードツールからペーストすると、クリップボードツールから疑似入力された Ctrl + V が効かなくなる。  
  Ditto の場合はレジストリ設定により、Shift + Insert を送信することでそのままペーストできるようになる。
  ```
  Windows Registry Editor Version 5.00

  [HKEY_CURRENT_USER\Software\Ditto\PasteStrings]
  "gvim.exe"="\"{PLUS}gP"
  "pwsh.exe"="+{INS}"
  "ssh.exe"="+{INS}"
  "OpenConsole.exe"="+{INS}"
  "conhost.exe"="+{INS}"
  "WindowsTerminal.exe"="+{INS}
  ```
  どのコマンドが実行されているかは、Windows Poershell を 1つだけ起動して(Powershell がいくつも開いていると、タスクマネージャーでどれかわからなくなるため)、タスクマネージャーでそれらしきプロセスを右クリックし、「ファイルの場所を開く」で確認。  
  (参考) https://github.com/sabrogden/Ditto/wiki/Custom-Key-Strokes

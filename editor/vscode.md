# Visual Studio Code

Table of Contents
=================

   * [Table of Contents](#table-of-contents)
      * [ショートカット](#ショートカット)
         * [移動系](#移動系)
         * [選択系](#選択系)
         * [編集系](#編集系)
         * [検索](#検索)
         * [置換](#置換)
         * [対象選択系](#対象選択系)
         * [表示系](#表示系)
         * [ファイル系](#ファイル系)
         * [比較系](#比較系)
         * [その他](#その他)
      * [設定変更](#設定変更)
         * [エディターの背景色変更](#エディターの背景色変更)
         * [markdownファイル編集時に行末をトリムしない](#markdownファイル編集時に行末をトリムしない)
      * [Tips](#tips)
      * [Plugins](#plugins)
         * [Bookmarks](#bookmarks)
         * [vscode-input-sequence](#vscode-input-sequence)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## ショートカット
### 移動系

|動作|キー操作|
|---|--------|
|単語単位で移動|option + control + ←→|
|対かっこに移動|command + shift + \ |
|定義に移動|F12|
|前に戻る|control + -|
|先に進む|control + _(shiftキーは不要)|

### 選択系

|動作|キー操作|
|---|--------|
|行選択|command + I|
|複数行カーソル|command + option + ↑↓|
|カーソル位置にある単語を一括選択|command + shift + L|

### 編集系

|動作|キー操作|
|---|--------|
|行複製|option + shift + ↑↓|
|行移動|option + ↑↓|
|ソート(昇順)|1. 範囲選択<br>2. command + shift + P<br>3. Sort Lines Ascending|
|ソート(降順)|1. 範囲選択<br>2. command + shift + P<br>3. Sort Lines Descending|

### 検索

|動作|キー操作|
|---|--------|
|検索|command + F|
|単語検索|command + option + W|
|正規表現|command + option + R|
|大文字・小文字区別|command + option + C|
|フォーカスをエディタに残したまま、検索ボックスを表示?|command + E|
|置換|command + option + F|
|grep|command + shift + F|
|grep オプション表示|command + shift + J|


### 置換

|動作|キー操作|
|---|--------|
|置換開始|command + option + F|
|一括置換|command + option + Enter|

### 対象選択系

|動作|キー操作|
|---|--------|
|エディタ内のシンボルの一覧を表示|command + shift + O|

### 表示系

|動作|キー操作|
|---|--------|
|サイドバーの表示/非表示|command + B|
|ファイル一覧を表示|command + shift + E|
|マークダウンプレビュー|command + shift + V|
|エディター表示を分割|command + option + control + \\
|ファイルのタブを表示・非表示切り替え|command + control + W|
|問題ビューの表示・非表示|command + shift + M|
|ターミナルを表示/非表示|command + J<br>control + shift + @|


### ファイル系

|動作|キー操作|
|---|--------|
|ワークスペースのファイル一覧を表示して切り替える|command + P|
|過去に開いたことのあるディレクトリ、ファイルを表示|control + R|

### 比較系
|動作|キー操作|
|---|--------|
|diff|command + shift + P, Compare Active File With ...|

### その他

|動作|キー操作|
|---|--------|
|タブ(ファイル一覧)表示,切替え|control + TAB|
|他のタブを閉じる|command + option + T|
|サイドバーにフォーカスを移動|command + 0|
|ワークスペースでカーソル位置にあるファイルを開く|command + ↓|
|一番左のエディタにフォーカスを移動|command + 1|
|ウィンドウの一覧を表示して切り替え|control + w|

## コマンド

### Markdown ファイルを HTMLファイルに変換

1. Markdown ファイルを開く
2. Command + Shift + P
3. Markdown PDF: Export(html) を選択

## 設定変更
### エディターの背景色変更

~/Library/Application Support/Code/User/settings.json
```
"workbench.colorCustomizations": {
    "editor.background": "#000020",
},
```  
(参考)  
[Visual Studio Codeの設定](https://www.atmarkit.co.jp/ait/articles/1709/15/news027_4.html)  
[Visual Studio Code Theme Color](https://code.visualstudio.com/api/references/theme-color)

### markdownファイル編集時に行末をトリムしない

~/Library/Application Support/Code/User/settings.json が次のようになっているところを
```
"files.trimTrailingWhitespace": true
```
次のように編集
```
"[markdown]": {
    "files.trimTrailingWhitespace": false
},
"files.trimTrailingWhitespace": true
```

## Tips
* JSON を書式化して表示  
  * command + k, m で書式の一覧を表示して JSON を選択。
  * 右クリックして「Format Document」を選択。

## Plugins
### Bookmarks

https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks

|機能|キー|
|---|---|
|トグル|command + option + K|
|前のブックマークへ|command + option + J|
|次のブックマークへ|command + option + L|

### vscode-input-sequence

|項目|内容|
|:--|:--|
|開始|範囲選択して、command + option + 0 で開始|
|指定|開始値 [ステップ [[符号]ステップ][ : 桁数][ : 基数]|
|1 : 2|1から開始して 2桁で|
|1 1 : 2|上と同じ(ステップを指定)|
|1 10 : 3|1から開始して 10おきに 3桁で|
|1 1 : 2 : 16|1から開始して 2桁の 16進数で|
|10 -1 : 2|10から開始して -1 ずつ 2桁で|

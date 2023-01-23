# Sublime Text 3

Table of Contents
---

* [Sublime Text 3](#sublime-text-3)
   * [表示](#表示)
   * [編集](#編集)
   * [HTML](#html)
   * [クリップボード](#クリップボード)
   * [ファイル](#ファイル)
   * [検索・移動](#検索移動)
   * [置換](#置換)
   * [マークダウン](#マークダウン)
   * [その他](#その他)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)
---

## 表示

* ファイルタブ移動
  * 右のファイルのタブに移動  
Command + Option + →  
または  
Command + Shift + }
  * 左のファイルのタブに移動  
Command + Option + ←  
または  
Command + Shift + {  
* 折りたたみ
  * 選択範囲を折りたたみ  
Command + Option + [
    * 折りたたみ解除  
Command + Option + ]
* サイドバーにフォーカスを移動  
control + 0(ゼロ)
* カーソル位置の文字コードを表示  
Command + P で Install Package を選択し、Show Character Code をインストール。

## 編集

* 行削除  
Control + Shift + K  
Command + X(コピーした行が、クリップボードにコピーされる)
* 行移動  
Command + Control + カーソル上下
* 行複製  
Command + Shift + D
* 行選択  
Command + L
* 行を連結  
Command + J
* カーソル位置から行末まで削除  
Command + K, Command + K
* カーソル位置から行頭まで削除  
Command + K, Command + Backspace
* 複数カーソル(同じ桁位置)  
Control + Shift + カーソル上下
* 複数行の先頭に文字(列)を追加挿入  
  * 挿入する行を範囲選択
  * Command + Shift + L でカーソルを行末に移動
  * Ctrl + A でカーソルを行頭に移動
  * 挿入したい文字(列)を入力
  * 行末にも文字列を追加したい場合は、Ctrl + E でカーソルを行末に移動して入力
  * ESCで範囲選択を終了
* 連番挿入
* InputSequence をインストール  
Package Control から Add Repository で https://github.com/kazu1107/InputSequence.git を追加  
Install Package で InputSequence をインストール
* 複数キャレット状態で Control + Shift + 0
* 必要に応じて Sequence Format: を指定(例: 00000)  
https://github.com/kazu1107/InputSequence
* 大文字に変換  
Command + K, Command + U
* 小文字に変換  
Command + K, Command + L
* カーソル位置の単語を1つずつ選択  
Command + D
* カーソル位置の単語を一括して選択  
Command + Control + G
* インデント  
Command + [ (左にインデント)
Command + ] (右にインデント)
* 行選択  
Command + L
* 対応する括弧の中身を選択  
Control + Shift + M
* yank(ヤンク)  
削除: 範囲選択して option + delete(fn + delete)  
ヤンク: control + y  
行末削除(control + k)、行削除(control + shift + k)もkill ring に入る
* 複数カーソルを任意の位置に設定  
Command + クリック を必要な箇所だけクリックしていく
* キーボードマクロ  
開始: control + Q  
終了: control + Q  
再生: control + shift + Q

## HTML

* 終わりの HTMLタグを挿入  
command + option + .  
(例) <td> と入力したキー操作すると、</td> が挿入される。

## クリップボード

* 行をコピー
非選択状態で Command + C
* 行カット
非選択状態で Command + X

## ファイル

* ファイルを開く
Command + P

## 検索・移動

* 指定行にジャンプ  
control + G
* grep検索  
command + Shift + F
* 対括弧にジャンプ  
control + M
* シンボル、メソッドにジャンプ  
command + R
* 定義にジャンプ  
command + option + カーソル下
* ジャンプ前の位置に戻る  
control + -
* 文字列検索  
command + P, #
* カーソル位置を維持したまま、スクロール  
control + option + カーソル上下
* カーソル位置が中央に来るようにスクロール  
control + L
* インクリメンタルサーチ  
command + I
* カーソル位置の単語を検索  
command + option + G

## 置換

* 置換
Command + Option + F

## マークダウン

* インストール  
Packege Control: Install Package  
OmniMarkupPreviewer
* プレビュー
Command + option + O
* HTML出力
Command + option + X
* HTML をクリップボードにコピー
control + option + C

* diff

* 比較開始
control + option + D
* キーバインド  
/ : ナビゲーター表示  
↓ : 次の差異  
↑ : 前の差異  
← : 右から左へマージ  
→ : 左から右へマージ  
control + Enter : 編集モード

## その他

* F7キー(build)を無効にして、日本語入力中に F7キーでカタカナに変換できるようにする。
  * /Library/Application Support/Sublime Text 3/Packages ディレクトリの下に Default ディレクトリを作成
  * メニューから [Preference] - [Key Bindings - Default] を開く
  * [File] - [Save] で保存する
    * ~/Library/Application Support/Sublime Text 3/Packages/Default/Default (OSX).sublime-keymap が作成される
  * ~/Library/Application Support/Sublime Text 3/Packages/Default/Default (OSX).sublime-keymap をエディタで開く  
    Sublime Text で開いても良い
  * 「{ "keys": ["f7"], "command": "build" }」という今日があるので、行頭に // を追加してコメントアウトする
  * 保存する
  * Sublime Text を再起動する

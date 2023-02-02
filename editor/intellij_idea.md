# IntelliJ IDEA

Table of Contents
-----------------

   * [Table of Contents](#table-of-contents)
      * [ショートカット](#ショートカット)
         * [移動系](#移動系)
            * [カーソル移動](#カーソル移動)
            * [マーク系](#マーク系)
         * [表示系](#表示系)
         * [コード編集系](#コード編集系)
         * [テキスト編集系](#テキスト編集系)
         * [検索系](#検索系)
         * [その他](#その他)
      * [トラブルシューティング](#トラブルシューティング)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## ショートカット

### 移動系

|動作|キー操作|
|---|-------|
|指定した行番号にジャンプ|command + L|
|対括弧にジャンプ|control + M|
|コードブロックの開始位置にジャンプ|command + option + [|
|コードブロックの終了位置にジャンプ|command + option + ]|
|クラスやメソッド、変数の定義にジャンプ|command + B|
|インターフェースメソッドの実装箇所にジャンプ|command + option + B|
|次の定義/前の定義に移動|control + ↓/control + ↑|
|最後の編集位置に移動|command + shift + backspace|

#### カーソル移動

|動作|キー操作|
|---|-------|
|右|control + F|
|左|control + B|
|行頭|control + A|
|行末|control + E|
|上|control + P|
|下|control + N|

#### マーク系

|動作|キー操作|
|---|-------|
|行に番号をマークする|control + shift + 1〜9|
|マークした番号に移動|control + 1〜9|
|カーソル位置の行をブックマーク(トグル)|F3|
|ブックマーク一覧を表示|command + F3|

### 表示系

|動作|キー操作|
|---|-------|
|変数の型を表示(マウス操作)|commandキーを押しながらマウスポインターを変数の場所に合わせる|
|変数の詳細を表示|command + shift キーを押しながらマウスポインターを変数の場所に合わせる|
|メソッドや変数の定義を簡易表示|control + J または F1|
|メソッドや変数の内容をダイアログ表示|command + Y|
|ビルド結果の表示を隠す|shift + escape|
|クラス階層を表示|control + H|
|プロジェクトに対して最近行った変更をリスト|option + shift + C|
|最近開いたファイルの一覧を表示|Command + E|
|最近編集したファイルの一覧を表示|Command + Shift + E|

### コード編集系

|動作|キー操作|
|---|-------|
|行コメント|command + /|
|ブロックコメント|command + option + /|
|入力しながら一致するメソッドやフィールドをリスト|command + option + O|
|importを最適化|control + option + O|
|自動インデント|control + option + I|
|if, while, when などを入力して {} を自動追加|command + shift + Enter|

### テキスト編集系

|動作|キー操作|
|---|-------|
|行頭まで削除|command + backspace|
|行末まで削除|control + K|
|行結合|control + shift + J|
|下に行挿入|shift + enter|
|上に行挿入|command + option + enter|
|カーソル位置の単語を選択、さらに同じ単語を順次複数選択|control + G|
|複数行カーソル|option + shift + マウスクリック|
|カーソル位置と一致する単語をすべて選択|option + command + G|
|行複製|command + D|
|行削除|command + delete|
|行移動(インデント補正なし)|option + shift + ↑↓|
|行移動(インデント補正付き)|command + shift + ↑↓|
|カーソル位置の単語を選択|control + G|

### 検索系

|動作|キー操作|
|---|-------|
|カーソル位置の単語を選択|control + G|
|カーソル位置の文字列を検索(1)|文字列を選択して command + F|
|カーソル位置の文字列を検索(2)|control + G, command + G/command + shift + G|
|次を検索|command + G, またはカーソル下|
|前を検索|command + shift + G, またはカーソル上|

### その他

|動作|キー操作|
|---|-------|
|リファクタリングメニュー表示|control + T|

* カーソル位置にある変数の使用箇所を強調表示する色の設定を変更  
[Preference] - [Editor] - [Color Scheme] - [General] の identifier under caret の色を変更する

## トラブルシューティング

* Gradle プロジェクトで `Cannot resolve symbol` というエラーが出る  
定義されているクラスの解決ができない  
メニューから File > `Invalidate Caches / Restart...` を選択して実行
* Gradle プロジェクトで外部参照しているプロジェクトのソースが更新されても反映されない  
参照しているプロジェクトで `gradle build -x test` してみる。
* Gradle プロジェクトで、依存 JAR ファイルが参照できないエラーが発生している。  
build.gradle があるディレクトリで、`gradle build -x test` してみる。
* Gradle プロジェクトで「Task 'wrapper' not found in project」というエラーが発生する。  
[Preferences] - [Build, Execution, Deployment] - [Gradle] - [Use Gradle from] で [Sepecified location] を選択し、Gradle のインストールディレクトリを指定する。
* [Spring initializer](https://start.spring.io) で作成した Kotlin + Gradle プロジェクトを IDEA にインポートして開くと、「import org.jetbrains.kotlin.gradle.tasks.KotlinCompile」の行で "jetbrains" が「cannot resolve」というメッセージとともに赤く表示される。  
Preferenes(Command + ,) - [Build, Execution, Deployment] - [Build Tools] - [Gradle] で [Gradle user home] に Gradle 7.6 のパスを指定する。  
=> /Users/xxx/.gradle/wrapper/dists/gradle-7.6-bin/9l9tetv7ltxvx3i8an4pb86ye/gradle-7.6/  
(元は 6.6 の指定になっていた。おそらく前のバージョンの IDEA で使っていた Gradle のパス。)

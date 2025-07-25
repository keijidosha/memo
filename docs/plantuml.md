- Table of Content  
{:toc}

# plantuml

[PlantUML](http://plantuml.com/)

# はじめに
* 特定のディレクトリにあるファイルを一括変換  
  ```
  java -jar plantuml.jar -tsvg -filedir ~/hoge/
  ```
* 常駐監視してファイルが保存されるとSVG出力  
  ```bash
  #!/bin/sh
  java -jar plantuml.jar -gui -tsvg
  ```
* ファイル名  
ファイル名の拡張子を html などにしておくと、エディタの補完が効く  
(例) xxx.pu.html

* 開始と終了  
  ```
  @startuml
  ...
  @enduml
  ```

# シーケンス図
* 呼び出し  
  `ClassHoge->ClassFuga: method(param)`
* 非同期呼び出し  
  `ClassHoge->>ClassFuga: method(param)`
* 戻り値  
  `ClassHoge<--ClassFuga: ReturnValue`
* 非同期戻り値  
  `ClassHoge<<--ClassFuga: ReturnValue`
* パラメータや戻り値でジェネリック(List<String>)を表現  
  `ClassFuga-->ClassHoge: List&lt;String&gt;`
* 特定条件の処理(条件分岐)  
opt を使う
  ```
  opt null
      ClassHoge->Logger: warn(message)
  end
  ```
* ループ処理
loop を使う  
  ```
  loop not eof
      ClassHoge->File: read()
  end
  ```
* クラスが呼び出される前に配置する  
`participant SomeClass`
* クラスを活性化する  
`activate SomeClass`
* クラスを非活性化する  
`deactivate SomeClass`
* クラスを破棄する  
`destroy SomeClass`


## 色設定
```
skinparam sequence {
    ArrowColor Blue
    ParticipantBorderColor DarkBlue
}
```
```
skinparam sequence {
    ArrowColor #000000
    LifeLineBorderColor #000000
    LifeLineBackgroundColor #1695a3
    ParticipantBorderColor #000000
    ParticipantFontColor #0000ff
}
```

## ノート
* クラスの中央にノート貼り付け  
  `note over ClassHoge: xxx`
* クラスの右側にノート貼り付け  
  `note right of ClassHoge: xxx`
* クラスの左側にノート貼り付け  
  `note left of ClassHoge: xxx`
* ノートの途中で改行  
  `note right of ClassHoge: xxx\nxxx`

### マークダウン
改行と組み合わせて使える
* 見出し1: `= xxx`
* 見出し2: `== xxx`
* 見出し3: `== xxx`
* 箇条書き: `* xxx`
* 数字付き箇条書き: `# xxx`
* ハイパーリンク: `[[https://xxx Label]]`  
  * ラベルなしの場合: `[[https://xxx]]`



## その他
* タイトル  
  `title シーケンス図のタイトル`
* コメント  
'(シングルクォート)から行末まで  
  `'コメント`  
複数行のコメントは次のように
  ```
  /'
  コメント
  '/
  ```
* ディバイダ  
見出し付き水平線を引く  
`== 見出し ==`

[Atom+PlantUMLで見た目もいい感じのシーケンス図を作成する](https://qiita.com/k_nakayama/items/77ca73753ebd049a66de)

# クラス図
* クラス、インターフェース
  ```
  interface Hoge {
      -privateMethod(param: String)
      +publicMethod(param: Int)
  }

  class Fuga {
      -privateField: String
      +publicField: Int
      +list: List<String>
      -privateMethod(param: String): String
      +publicMethod(param: Int): Int
      +{static} staticMethod(param: String): String
      +{abstract} abstractMethod(param: String): String
  }
  ```

* 接続  
  ```
  ClassHoge-ClassFuga
  ClassHoge--ClassFuga
  ClassHoge-->ClassFuga
  ClassHoge "1" *-- "0..*" ClassFuga
  ClassHoge *-  ClassFuga
  ClassHoge *--  ClassFuga
  ClassHoge o-  ClassFuga
  ClassHoge..ClassFuga
  ClassHoge<|.ClassFuga
  ClassHoge<|..ClassFuga
  ```

* 色設定  
  ```
  skinparam class {
    ArrowColor Blue
    BackgroundColor LightYellow
    BorderColor DarkBlue
  }
  ```

(参考) [もう辛くない！テキストで書くUML クラス図編](https://qiita.com/ykawakami/items/f6688b845945669f0ce5)

### 出力形式

* -teps: To generate images using EPS format
* -thtml: To generate HTML file for class diagram
* -tlatex:nopreamble: To generate images using LaTeX/Tikz format without preamble
* -tlatex: To generate images using LaTeX/Tikz format
* -tpdf: To generate images using PDF format
* -tpng: To generate images using PNG format (default)
* -tscxml: To generate SCXML file for state diagram
* -tsvg: To generate images using SVG format
* -ttxt: To generate images with ASCII art
* -tutxt: To generate images with ASCII art using Unicode characters
* -tvdx: To generate images using VDX format
* -txmi: To generate XMI file for class diagram


## その他

### 文字装飾
* 文字色を設定  
  `ここから<color:red>赤文字</color>がここまで`  
  ※行末まで同じ文字色にする場合は、`</color>` は省略可能。明示的に終わりを示したい場合だけ指定すれば良い。
* 全体の文字色を指定
  `skinparam defaultFontColor magenta`
* 背景色を設定  
  `ここから<back:aqua>背景水色</back>がここまで`
* note の背景色を設定  
  `note right of hoge #aqua: 説明`  
  : の前に色を指定(色名または色コード)
* ボールド  
  `ここから<b>ボールド</b>がここまで`
* ボールドで文字色を設定  
  `ここから<color:red><b>赤文字</color>がここまで`  
  `</color>` があるので、`</b>` は省略可能
* 下線  
  `ここから<u>下線</u>`
* 波線の下線
  `ここから<w>波下線</w>`
* 取り消し線  
  `ここから<s>取り消し線</s>`
* フォント: 等幅フォントを指定  
  `ここから<font:Courier>等幅フォント</font>`  
  Courier, Monospaced などがプラットフォーム非依存フォントとして指定可能
* フォントサイズ  
  `ここから<size:16>16ptフォント</size>`

### リンク
* URL リンク  
  `[["<URL>" 表示文字列 ]]`

### ヘルプを表示

```
java -jar plantuml.jar -h
```

## トラブルシューティング

* サイズの大きな画像(PNG)を出力しようとすると、途中で切れてしまう。  
  デフォルトの画像の上限が 4096 なので、上限を緩和する。  
  `export PLANTUML_LIMIT_SIZE=8192`  
  これによりメモリー不足になった場合は、メモリーの上限を緩和する。  
  `-Xmx1024m`  
  (参考)  
  [PlantUML FAQ](https://plantuml.com/ja/faq)  
  [PlantUMLのpng画像が切れてしまう](https://www.pospome.work/entry/20160422/1461322513)  


# plantuml

Table of Contents
=================

* [はじめに](#はじめに)
* [シーケンス図](#シーケンス図)
   * [ノート](#ノート)
      * [マークダウン](#マークダウン)
   * [その他](#その他)
* [クラス図](#クラス図)

[PlantUML](http://plantuml.com/)

# はじめに
* 常駐監視してファイルが保存されるとSVG出力  
  ```bash
  #!/bin/sh
  java -jar /xxx/lib/plantuml.jar -gui -tsvg
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
`participannt SomeClass`
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
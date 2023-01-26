# AsciiDoc


## 書式
* 目次と章立て、イメージディレクトリ  
  ```
  :toc:
  :toclevels: 2
  :toc-title: Table of Contents
  :sectnums:
  :chapter-label:
  :imagesdir: images/
  :author: Hoge
  :revnumber: 1.0
  :revdate: 2018/01/01
  :lang: ja
  :doctype: book
  ```
  sectnums を付けて PDF 化すると、各章のタイトルに「Chapter」と文字列が付与される。そこでパラメータ指定のない chapter-label を追加することで「Chapter」消去する。
* 箇条書き  
  ```
  * 項目1 +
  内容11 +
  内容12
  * 項目2 +
  内容21 +
  内容22
  ```
  半角スペース1個と+(プラス記号)で改行する
* 数字の箇条書き  
  ```
  . 項目1 +
  内容11 +
  内容12
  .. 内容121
  .. 内容122
  . 項目2 +
  内容21 +
  内容22
  ```
  インデントした数字の箇条書きは、「..」のように、複数記述する。
* 箇条書きの中でコード引用する  
  `[source]` を使うと、箇条書きの中で引用できる  
  => 次の箇条書き項目まで引用の対象となる  
  ```
  * 項目1 +
  [source]
  int ii = 0;
  string ss = "hoge";
  ```
* タイトルと引用の組み合わせ(ファイルの内容を説明する時など)  
  ```
  .タイトル
  ----
  引用文1
  引用文2
  ----
  ```
* タイトルをセンタリングして、タイトルと内容を枠で囲む
  ```
  .タイトル
  ****
  引用文1
  引用文2
  ****
  ```
* タイトルの前に「Example n.」が付き、内容を枠で囲む
  ```
  .タイトル
  ====
  引用文1
  引用文2
  ====
  ```
* テーブル  
  ```
  .テーブルタイトル
  [options="autowidth"]
  |===
  |タイトル1|タイトル2|タイトル3
  
  |明細1-1|明細1-2|明細1-3
  |明細2-1|明細2-2|明細2-3
  |===
  ```
  * 2行結合  
`.2+` を行頭に記述  
(例)  
`.2+|Apple|iPhone`
  * 2列結合  
`2+` を列の前に記述  
(例)  
`|Apple 2+|iPhone`
* 文字色  
`[red]#色を付けたい文字#`  
この方法では PDF に反映されない。=> 最新版の asciidoctor では反映された  
(回避策)  
`pass:[<span style="color:#ff0000">色を変えたい文字</span>]`
* 強調表示(マーカー)  
`#強調したい文字#`
* 脚注  
  footnote を使います。  
  `これはAsciiDoctorfootnote:[テキストファイルからHTMLやPDFに変換するツール]の説明です。`
* 水平線  
`---`
* 改ページ  
`<<<`
* イメージファイル挿入  
`image::<パス>[]`  
(例) `image::./hoge.png[]`
* 注意書きなど  
次の項目が使用可能  
NOTE, TIP, IMPORTANT, WARNING, CAUTION  
書き方
  * その1  
    ```
    NOTE: 内容
    ```
  * その2  
    ```
    .タイトル(省略可)
    NOTE: 内容1
      内容2
    ```
  * その3  
    ```
    .タイトル(省略可)
    [NOTE]
    内容1
    内容2
    ```
  * その4  
    ```
    [NOTE]
    ====
    内容1
    内容2
    ====
    ```
  どの書き方でも同じ形式でフォーマットされる模様
## 変換
* HTMLに変換  
`asciidoctor -d book -o hoge.html -n hoge.adoc`
* PDF に変換  
`asciidoctor-pdf -r asciidoctor-pdf-cjk hoge.adoc -o hoge.pdf`

## 環境構築
* `gem install asciidoctor`
* `gem install --pre asciidoctor-pdf`
* `gem install asciidoctor-pdf-cjk`

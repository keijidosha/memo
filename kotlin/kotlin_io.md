# Kotlin I/O(ファイル,ネットワーク等)

## Reader拡張いろいろ

### forEachLine

* 1行ずつ読み込み、ブロックが実行される
  ```kotlin
  FileReader( "hoge.txt" ).reader.forEachLine{
      println( it )
  }
  ```

### readLines

* 各行をリストで返す
  ```kotlin
  val lines = FileReader( "hoge.txt" ).readLines()
  for( str in lines ){ println( str ) }
  ```

### useLines

シーケンスを生成してブロックが実行される  
シーケンスなので、1行ずつ読み込みながらブロックが実行される

* ブランクでない行だけのリストを作成  
`val list = File( "hoge.txt" ).reader().useLines{ it.filter(String::isNotBlank).toList() }`
* 1文字以上ある行だけのリストを作成  
`val list = File( "hoge.txt" ).reader().useLines(){ it.filter{str -> str.length > 0}.toList() }`
* 最初の3行のリストを作成(4行目以降は読み込まれない)  
`val list = File( "hoge.txt" ).reader().useLines(){ it.take(3).toList() }`
* 各行の内容を表示  
`File( "hoge.txt" ).reader().useLines(){ it.forEach{ println( it ) } }`

※バイナリファイルを Reader の useLines で読み込むと例外(MalformedInputException)がスローされるが、File の useLines で読み込むと例外はスローされない。

### readText

* ファイルの内容を文字列で返す  
`val text = FileReader( "hoge.txt" ).readText()`

## その他 java.io 拡張

* WEBページの内容を 1行ずつ処理してみる  
`URL( "http://www.yahoo.co.jp/" ).openStream().bufferedReader().lineSequence().forEach{ println( it ) }`  
fun InputStream.bufferedReader( charset: Charset = Charsets.UTF_8 ): BufferedReader  
fun BufferedReader.lineSequence(): Sequence<String>

## HTTP(などURL系)

* 指定した URL に接続して、内容を取得  
`println( URL( "http://www.yahoo.co.jp/" ).readText())`
* バイナリデータを受信する場合は readBytes を使う  
(全データを受信するので、データ量に注意)  
`val bytes = URL( "http://www.yahoo.co.jp/" ).readBytes()`

## リソース

* リソース(CLASSPATH)からファイルを読み込む  
`val text = javaClass.getResourceAsStream( "/hoge.txt" ).use() { itreader().readText() }`  
use を使わないと、InputStream が close されないままになってしまう(リソース解放漏れと、ファイルロックが残ってしまう)

## ファイル拡張

* inputStream
* outputStream
* reader  
Reader を取得  
  ```kotlin
  File( "hoge.txt" ).reader().use() {
      val sb = StringBuilder()
      val buf = CharArray(64)
      var len = -1
      while( run { len = it.read( buf, 0, 64 ); len >= 0 } ) {
          sb.append( buf, 0, len )
      }
      println( sb.toString())
  }
  ```
* Reader拡張 + use() から return して直接結果を変数に代入する  
  ```kotlin
  val html = URL("http://host/").openStream().reader().use() {
      return@use it.readText()
  }
  println(html)
  ```
* bufferedReader  
BufferedReader を取得  
  ```kotlin
  File( "hoge.txt" ).bufferedReader().use() { reader ->
   var str = reader.readLine()
   while( str != null ) {
   println( str )
   str = reader.readLine()
   }
  }
  ```
* writer  
Writer を取得  
`File( "hoge.txt" ).writer().use() { it.write( "hoge\n" ) }`
* bufferedWriter  
BufferedWriter を取得  
`File( "hoge.txt" ).bufferedWriter().use() { it.write( "hoge" ); it.newLine() }`
* printWriter  
printWriter を取得する  
`File( "hoge.txt" ).printWriter().use(){ it.println( "hoge" ) }`
* readText  
ファイル内容を取得する  
`println( File( "hoge.txt" ).readText())`
* readBytes
* writeBytes
* writeText  
ファイルに文字列を書き出す。  
`File( "hoge.txt" ).writeText( "hoge\n" )`
* appendText  
ファイルに文字列を追記する。ファイルが存在しない場合は作成される。  
`File( "hoge.txt" ).appendText( "Hoge!\n" )`
* copyTo  
ファイルをコピー  
`File( "/var/log/hoge.log" ).copyTo( File( "/tmp/hoge.txt" ), true )`  
true: 上書きする, 省略時は false。false でコピー先が存在した場合は「FileAlreadyExistsException」が投げられる  
タイムスタンプは維持されない。コピーした時のタイムスタンプになる。  
* copyRecursively  
ディレクトリ以下を別のディレクトリにコピー  
`File( "/tmp" ).copyRecursively( File( "/home/hoge" ), true )`  
true: 上書き、false: すでにファイルがあると kotlin.io.FileAlreadyExistsException がスローされる  
ラムダ式を書くと、エラー処理を記述できる  
/tmp 以下にあるファイル、ディレクトリを /home/hoge にコピー  
==> /home/hoge に tmp ディレクトリは作成されない  
* deleteRecursively  
ディレクトリ以下を削除  
`File( "/tmp/hoge" ).deleteRecursively()`
* extension  
ファイルの拡張子を取得  
`File("hoge.txt").extension    // ==> "txt"`
* forEachBlock  
バイト配列と長さを受け取るブロックが呼び出される  
`File( "hoge.txt" ).forEachBlock{ buf, size -> println( String( buf, 0, size )) }`
* forEachLine  
ブロックに1行ずつ渡される  
`File( "hoge.txt" ).forEachLine{ println( it ) }`
* readLines
* useLines
* isRooted  
パスがルートディレクトリからのパスを表しているか  
  ```kotlin
  File("/hoge/fuga.txt").isRooted    // true
  File("../fuga.txt").isRooted    // false
  ```
* nameWithoutExtension  
ディレクトリパスと拡張子を取り除いたファイル名  
`File("/fuga/hoge.txt").nameWithoutExtension    // "hoge"`
* walk  
パス以下のファイル、ディレクトリを再帰的に探索する FileTreeWalk を返す  
forEach ブロックには java.io.File オブジェクトが渡される  
`File( "/tmp" ).walk().forEach{ println( it.absolutePath ) }`
FileTreeWalk に対して maxDepth(1) を指定すると、直下(1階層)だけ探索する  
`File( "/tmp" ).walk().maxDepth(1).forEach{ println( it.absolutePath ) }`
* 拡張子 txt のファイルだけを取り出す  
`File( "/tmp" ).walk().filter{ it.extension == "txt" }.forEach{ println( it.absolutePath ) }`
* フルパス名で並び替える  
`File( "/tmp" ).walk().sorted().forEach{ println( it ) }`
* ファイル名で並び替える  
`File( "/tmp" ).walk().filter{ it.isFile() }.sortedBy{ it.name }.forEach{ println( it ) }`
* 拡張子で並び替える  
`File( "/tmp" ).walk().filter{ it.isFile() }.sortedBy{ it.extension }.forEach{ println( it ) }`
* sortedWith を使って(Comparator を渡して)拡張子で並び替える
  ```kotlin
  import java.util.*
  
  File( "/tmp" ).walk().filter{ it.isFile() }.sortedWith( Comparator{ a, b -> a.extension.compareTo( b.extension ) } ).forEach{ println( it ) }
  ```
* ブロックに記述した方法でグループ化する。  
  例えば拡張子でグループ化する。  
  forEach には Map.Entry が渡される  
  ```kotlin
  File( "/tmp" ).walk().groupBy{ it.extension }.forEach{ println( "${it.key}:" ); it.value.forEach{ println( "  ${it.absolutePath}" )} }
  ```
  (結果)  
  ```kotlin
  class:
     hoge.class
     hogee.class
     fuga.class
     fungaa.class
  java:
     hoge.java
     fuga.java
  kt:
     hogee.kt
     fungaa.kt
  ```


## ファンクション

* createTempDir  
一時ディレクトリを作成  
  ```kotlin
  val tmpDir = createTempDir( "hoge" )
  println( tmpDir.absoluteName )
  tmpDir.delete()
  ```
* createTempFile  
一時ファイルを作成  
  ```kotlin
  val tmpFile = createTempFile( "hoge.txt" )
  println( tmpFile.absoluteName )
  tmpFile.delete()
  ```

## その他

* Kotlin で System.in を使う(標準入力を拾う)  
  in は for で使われる予約語 in と被っているため、バッククォートで囲む必要がある  
  (例) 標準入力から取得した文字列から空でない行を表示する(標準出力に渡す)  
  ```kotlin
  if( System.`in`.available() > 0 ) {
      System.`in`.bufferedReader().use {
          do {
              var str = it.readLine()
              if( str.isNullOrEmpty()) continue
              println( str )
          } while( str != null )
      }
  }
  ```


Table of Contents
=================
* [ループ](#ループ)
  * [for](#for)
  * [while](#while)
* [break, continue](#break-continue)
   * [forEach](#foreach)
   * [when](#when)
* [when](#when-1)
* [try/catch](#trycatch)
* [その他](#その他)

## ループ
* ループ文(while, for)や if 文に代入文は書けない  
(例)  
  ```kotlin
  var str: String
  while(( str = reader.readLine()) != null ) {    // <== コンパイルエラーになる
      // 処理
  }
  ```
  次のようにする  
  ```kotlin
  var str: String
  str = reader.readLine()
  while( str != null ) {
      // 処理
      str = reader.readLine()
  }
  ```
  次のような方法とか!  
  ```kotlin
  var str: String? = null
  while( reader.readLine().let{ str = it; it != null } ) {
      println( str )
  }
  ```
  次のような方法もある!  
  ```kotlin
  var str: String? = null
  while( run{ str = reader.readLine(); str != null } ) {
      println( str )
  }
  ```
  次のような方法もあるが、コンパイルすると、匿名クラス(classファイル)が作成されるので多用する場合は注意  
  ```kotlin
  var str: String? = null
  while( { str = reader.readLine(); str }() != null ) {
      println( str )
  }
  ```
### for
* 決められた回数繰り返す  
  下記はすべて同じ結果になる( 0 〜 2 まで繰り返す)  
  ```kotlin
  for( i in 0..2 ) { print( "$i, ") }
  ```
   ==> 0, 1, 2,  
  ```kotlin
  ( 0..2 ).forEach { print( "$it, ") }
  ```
  ==> 0, 1, 2,  
  ```kotlin
  repeat(3) { print( "$it, " )}
  ```
  ==> 0, 1, 2,  
* ステップ2 を指定  
  ```kotlin
  for( i in 1..10 step 5) println( i )
  ```
  ==> 1, 3, 5  
  ```kotlin
  (1..5 step 2).forEach{ println( it ) }
  ```
  ==> 1, 3, 5  
* 数字の大きい方から小さい方にループ  
  ```kotlin
  for( i in 3 downTo 1 ) println( i )
  ```
  ==> 3, 2, 1  
  ```kotlin
  ( 3 downTo 1 ).forEach{ println( it ) }
  ```
  ==> 3, 2, 1  
* ステップ 2 を指定  
  ```kotlin
  for( i in  5 downTo 1 step 2 ){ println( i ) }
  ```
  ==> 5, 3, 1  
  ```kotlin
  ( 5 downTo 1 step 2 ).forEach{ println( it ) }
  ```
  ==> 5, 3, 1

5 downTo 1 step 2 の IntProgression は Iterable なので、forEach の他にも map などのファンクションが使える  
Int.downTo は IntProgression を返す  
step は IntProgression のプロパティ

## while
whileループの中から一定の条件になった時、戻り値を返す  
(目的・条件)  
* メソッドは nullable な値を返さない  
  => メソッド呼び出し側で値が取得できたかどうかを判定しなくても良いようにする。値が取得できなかった場合は例外をスローする。  
* 「メソッド内で nullable な変数を宣言し、値が取得できた時にその変数にセットして、メソッドの最後に返す」といった、メソッド内で nullable な変数を使うことをしない  
 　=> 簡潔に書けるようにする


(例) WEBサーバから受信したヘッダの Last-Modified ヘッダの日付を返す
10回リトライして接続できない場合は例外をスローする => つまり null は返さない
```kotlin
fun getLastModified( url: String ): Date {
    var retryCount = 0

    while( true ) {
        try {
            val conn = URL( url ).openConnection()
            println( conn.inputStream.reader().use() { it.readText() }
            return Date( conn.lastModified )
        } catch( ex: Exception ) {
            if( retryCount == 10 ) {
                throw ex
            }
            println( ex.message )
        }
    }
}
```
* ポイント  
  while は true にする、つまり条件文にしない  
  「while( retryCount < 10 )」と書くとコンパイルエラーになる  
  while ループの中で break しない  
  while ループから抜けるようなコードを書くとコンパイルエラーになる  

# break, continue
## forEach
* forEach はループではなくラムダ式なので、continue や break は使えない  
  ```kotlin
  fun test {
      val list = listOf( 1, 2, 3 )
      // forEach の中で continue はコンパイルエラー
      list.forEach{
          if( it == 2 ) continue
          println( it )
      }
  
      // forEach の中で break もコンパイルエラー
      list.forEach{
          if( it == 2 ) break
          println( it )
      }
  
      // forEach の中で return@forEach を使うと直近の forEach に対する cotinue として使える
      list.forEach{
          if( it == 2 ) return@forEach
          println( it )
      }
  
      // for ループだと continue は問題なく使える
      for( ii in list ) {
          if( ii == 2 ) continue
          println( ii )
      }
  
      // for ループだと break も問題なく使える
      for( ii in list ) {
          if( ii == 2 ) break
          println( ii )
      }
  
      // forEach の中でラベル(@forEach)なしで return を指定すると、このメソッドを抜ける
      list.forEach{
          if( it == 2 ) return
          println( it )
      }
  }
  ```
* forEach 内で return すると、外側のメソッドを抜ける  
結果: 1, 3, 5  
  ```kotlin
  fun normalReturn()
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach{ ii->
          lst.forEach{ jj->
              if( jj % 2 == 0 ) return
              println( "${ii} : ${jj}" )
          }
      }
  }
  ```
* return @<関数名> でラムダ式を抜け、ブロックの先頭に戻る  
  ```kotlin
  fun labelReturn1()
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach{
          if( it % 2 == 0 ) return@forEach
          println( it )
      }
  
      println("-- labelReturn1 end")
  }
  ```
* return@<ラベル>でラムダ式を抜け、指定されたブロックの先頭に戻る  
  ```kotlin
  fun labelReturn2()
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach loop1@ { ii->
          lst.forEach loop2@ { jj->
              if( jj % 2 == 0 ) return@loop2
              println( "${ii} : ${jj}" )
          }
      }
  }
  ```
* 指定された外側のブロックに戻る  
  ```kotlin
  fun labelReturn3()
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach loop1@ { ii->
          lst.forEach loop2@ { jj->
              if( jj % 2 == 0 ) return@loop1
              println( "${ii} : ${jj}" )
          }
      }
  }
  ```
* ラベルを使わず、無名関数を使っても同じことができる  
  ```kotlin
  fun labelReturn4(lst: List<Int>)
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach{ ii->
          lst.forEach( fun(jj: Int) {
              if( jj % 2 == 0 ) return
              println( "${ii} : ${jj}" )
          })
      }
  }

  fun labelReturn5(lst: List<Int>)
  {
      val lst = listOf(1,2,3,4,5)
      lst.forEach( fun(ii: Int) {
          lst.forEach{ jj->
              if( jj % 2 == 0 ) return
              println( "${ii} : ${jj}" )
          }
      })
  }
  ```
* ラムダ式の中で break したい場合 run で囲んで return するという方法がある  
  ```kotlin
  run brk@ {
      Files.newDirectoryStream(Paths.get("/tmp"),"*.txt").use(){
          it.forEach{ path ->
              println(path)
              return@brk
          }
      }
  }
  ```
  /tmp ディレクトリで最初に見つかったファイル名だけが表示される
## when
* when の中では break, continue のままでは使えない  
そこでラベルを付ける  
  ```kotlin
  fun main(args: Array<String>) {
      var cnt = 0
  
      exit@ while(true) {
          cnt = cnt + 1
          when(cnt) {
              3 -> { continue@exit }  // while ループの先頭に戻る
              5 -> { break@exit }     // while ループを抜ける
          }
  
          println( cnt )
          Thread.sleep(1000)
      }
      println( "exit at $cnt" )
  }
  ```


# when
* when で変数の型を or 判定する場合、when に引数がある場合は ,(カンマ)で区切り、when に引数を指定せず when 内の構文で条件判断する場合は || で区切る。  
  ```kotlin
  fun main(args: Array<String>)
  {
      val hoge1 = "hoge"
      val hoge2 = 1
  
      sub(hoge1, hoge2)
  }

  fun sub(obj1: Any, obj2: Any)
  {
      when(obj1)
      {
          is String, is Int -> println("String/Int")
          else -> println("other")
      }
  
      when
      {
          obj2 is String || obj2 is Int -> println("String/Int")
          else -> println("other")
      }
  }
  ```

# try/catch
* try/catch 構造から値を返すこともできる  
  ```kotlin
  import java.net.*

  fun main( args: Array<String> ) {
      val str: String = try {
          URL("http://www.hoge.org/").readText()    // HTTPサーバから受信した文字列を返す
      } catch( ex: ConnectException) {
          ex.printStackTrace()
          return    // ConnectionTimeout が発生したら、処理を抜ける(str に文字列はセットされない)
         // return しておかないと、コンパイルエラーが出る(Unit を String に代入できない。Unit は printStackTrace() の戻り値)
         // それ以外の例外が発生した場合は、処理が中断される
      }
  
      println( str )
  }
  ```

# その他
* 自動的にクローズ  
(例) ファイルを読み込み終えたらクローズ  
  ```kotlin
  BufferedReader( FileReader( "hoge.txt" )).use { reader ->
      var line = reader.readLine()
      while( line != null ) {
          println(line)
          line = reader.readLine()
      }
  }
  ```
  「reader ->」を省略して it を使ってもよい

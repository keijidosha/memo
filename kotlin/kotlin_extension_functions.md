# Kotlin: 拡張関数

## 拡張プロパティ
* 簡単な例  
  ```kotlin
  val String.firstChar: Char
      get() = elementAt(0)
  val String.moreThan3Char: Boolean
      get() = length >= 3
  // 1行で書く
  val String.firstChar2: Char get() = elementAt(0)
  val String.moreThan3Char2: Boolean get() = length >= 3
  // 型推論で型は省略できる
  val String.firstChar2 get() = elementAt(0)
  val String.moreThan3Char2 get() = length >= 3
  ```
* 型が明確になっていれば、null に対しても拡張関数が呼ばれる  
  ```kotlin
  fun String?.greet() = if( this == null ) "You are null !" else "Welcome $this"
  
  fun main( args: Array<String> ) {
      var tom = "Tom"
      var mike: String? = null
      
      println( tom.greet())
      println( mike.greet())
  }
  ```


## 拡張関数を動的に切り替えるサンプル
### 方法1
```kotlin
// 拡張関数のインターフェースを定義
interface IntExtension {
    fun Int.double(): Int
}

// 2乗する拡張関数を定義
class IntImple1: IntExtension {
    override fun Int.double(): Int = this * this
}

// 3乗する拡張関数を定義
class IntImple2: IntExtension {
    override fun Int.double(): Int = this * this * this
}

// 4乗する拡張関数を定義
class IntImple3: IntExtension {
    override fun Int.double(): Int = this * this * this * this
}

fun main( args: Array<String> ) {
    // 呼び出し先でも拡張関数を利用するためには、同様に型の定義が必要
    val exec: IntExtension.() -> Unit = {
        println( 5.double())
    }

    // 拡張関数の実装クラスを利用する関数
    val run: IntExtension.() -> Unit = {
        println( 2.double())
        println( 3.double())
        exec( this )
    }

    // 拡張関数の型と文字列を引数で受け取る関数
    val run2: IntExtension.( String ) -> String = {
        println( 10.double())
        "hello " + it
    }

    // 拡張関数の型と 2つの文字列を引数で受け取る関数
    val run3: IntExtension.( String, String ) -> String = { msg1, msg2 ->
        println( 10.double())
        "hello " + msg1 + " " + msg2
    }

    // 2乗する拡張関数を使って実行
    run( IntImple1())
    println( "----------" )
    // 3乗する拡張関数を使って実行
    run( IntImple2())
    println( "----------" )
    // 拡張関数を文字列からインスタンス化して渡す
    val klass: IntExtension = Class.forName( "IntImple3" ).newInstance() as IntExtension
    run( klass )
    println( "----------" )
    // 拡張関数と文字列を渡す
    println( run2( IntImple1(),  "hoge" ) )
    println( "----------" )
    // 拡張関数と 2つの文字列を渡す
    println( run3( IntImple1(),  "hoge", "fuga" ))
}
```
実行結果
```
4
9
25
----------
8
27
125
----------
16
81
625
----------
100
hello hoge
----------
100
hello hoge fuga
```
(参考) [Kotlin で拡張関数をオーバーライドして実装を切り替えられるぞ！](http://vividcode.hatenablog.com/entry/kotlin/extension-functions-overridden)

### 方法2
```kotlin
interface IntExtension {
    fun Int.double(): Int
}

interface IntExtension_1: IntExtension {
    override fun Int.double(): Int = this * this
}
interface IntExtension_2: IntExtension {
    override fun Int.double(): Int = this * this * this
}
object IntExtension_1_obj: IntExtension_1
object IntExtension_2_obj: IntExtension_2

// IntExtension を実装したシングルトンオブジエクトを条件分岐で切替え
fun run( id: Int ) {
    val contextDouble = when( id ) {
        1 -> object: IntExtension_1 {}
        2 -> object: IntExtension_2 {}
        else -> throw Exception( "id $id is out of range" )
    }
    contextDouble.run {
        println( 3.double() )
        println( 5.double() )
    }

}

// クラス名から IntExtension を実装したシングルトンオブジエクトをインスタンス
fun run2( interfaceName: String ) {
    val contextDouble = Class.forName( interfaceName ).kotlin.objectInstance as IntExtension
    contextDouble.run {
        println( 6.double())
    }
}

fun main( args: Array<String> ) {
    run( 1 )
    println( "----------" )
    run( 2 )
    println( "----------" )
    run2( "IntExtension_1_obj" )
    println( "----------" )
    run2( "IntExtension_2_obj" )

    println( "----------" )

    IntExtension_1_obj.run {
        // IntExtension_1 で定義された double を実行
        println( 7.double())
        IntExtension_2_obj.run {
            // IntExtension_2 で定義された double を実行
            println( 7.double())
        }
    }
}
```
実行結果
```
9
25
----------
27
125
----------
36
----------
216
----------
49
343
```
(参考) [Kotlinの拡張関数のガイドライン ｜ Developers.IO](https://dev.classmethod.jp/smartphone/kotlin-extensions-guideline/)

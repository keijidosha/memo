- Table of Content  
{:toc}

# Kotlin: ファンクション

## 高階関数
2つの関数を渡す
```kotlin
fun main( args: Array<String> ) {
    // 丸括弧の中に 2つのラムダ式を記述する
    run( { println( "hoge" ) }, { println( "fuga" ) } )
    // 名前付き引数を使って渡す
    run( act2 = { println( "hoge" ) }, act1 = { println( "fuga" ) } )
}

fun <T> run( act1: () -> Any?, act2: () -> T ): T {
    act1()
    return act2()
}
```

## SAM変換
* Runnable を渡す  
  ```
  Executors.newSingleThreadExecutor().submit(
      object: Runnable {
          override fun run() {
              println("Hello")
          }
      }
  )
  ```  
  の代わりに  
  ```
  Executors.newSingleThreadExecutor().submit{ println( "Hello" ) }
  ```

## Callable Reference

* 引数が　1つ  
  `listOf("a", "ab", "abc").map(String::length)`
* 引数が 2つの例  
  `listOf(1,2,3,4,5).reduce(Int::plus)`  
  これは  
  `listOf(1,2,3,4,5).reduce { i, j -> i.plus(j) }`  
  や  
  `listOf(1,2,3,4,5).reduce { i, j -> i + j }`  
  と同じ
* Koltinドキュメントに記載されていた例
  * その1  
    ```
    fun isOdd(x: Int) = x % 2 != 0
    fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"
    val numbers = listOf(1, 2, 3)
    println(numbers.filter(::isOdd)) // refers to isOdd(x: Int), prints [1, 3]
    ```
  * その2  
    ```
    fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
        return { x -> f(g(x)) }
    }
    
    fun length(s: String) = s.length
    val oddLength = compose(::isOdd, ::length)
    val strings = listOf("a", "ab", "abc")
    println(strings.filter(oddLength)) // Prints "[a, abc]"
    ```
* ファンクション参照を使って Unix パイプ的に処理を流してみる  
    ```
    fun afterComma(str: String): String {
        val idx = str.indexOf(",")
        if( idx >= 0) {
            return str.substring(idx + 1)
        } else {
            return str
        }
    }

    fun toUpper(str: String) = str.toUpperCase()

    fun main( args: Array<String>) {
        "Hello,today is sunny day !".let(::afterComma).let(::toUpper).let(::println)
    }
    ```
* その2  
  ```
  fun hoge(p: Map.Entry<String, Int>) = p.key.substring(p.value)

  fun main( args: Array<String>) {
      val map = mapOf("Hello" to 3, "Good morning" to 5, "Good evening" to 7)
      map.map(::hoge).let(::println)
  }
  ```

## just an object
* ローカル変数としてオブジェクトを作成
  ```
  fun foo() {
      val adHoc = object {
          var x: Int = 0
          var y: Int = 0
      }
      print(adHoc.x + adHoc.y)
  }
  ```
* private function であれば戻り値に just an object が使える  
  ```
  class C {
      // Private function, so the return type is the anonymous object type
      private fun foo() = object {
          val x: String = "x"
      }
  
      // Public function, so the return type is Any
      fun publicFoo() = object {
          val x: String = "x"
      }
  
      fun bar() {
          val x1 = foo().x        // Works
          val x2 = publicFoo().x  // ERROR: Unresolved reference 'x'
      }
  }
  ```

  [Object Expressions and Declarations](https://kotlinlang.org/docs/reference/object-declarations.html)
  

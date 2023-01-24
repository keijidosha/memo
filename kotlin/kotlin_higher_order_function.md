# Kotlin: 高階関数

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

## ファンクション参照(Function Reference)

* 引数が　1つ  
  `listOf("a", "ab", "abc").map(String::length)`
* 引数が 2つも OK  
  `listOf(1,2,3,4,5).reduce(Int::plus)`
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

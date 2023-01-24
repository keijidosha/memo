# Kotlin: 文字列

## チェック

* null または空文字列かどうかを判定  
  ```kotlin
  if( hoge.isNullOrEmpty()) {
      println( "Nullか空文字列" )
  }
  ```
* null または空文字列、またはブランク(複数のスペース)かどうかを判定  
  ```kotlin
  if( hoge.isNullOrBlank()) {
      println( "Nullか空文字列かブランク" )
  }

## 文字数
* length() メソッドではなく、length プロパティ(クラス拡張)  
  ```println( "123".length )```

## 正規表現
* 電話番号を分解してみる  
  ```kotlin
  val reg = Regex( "(\\d+)-(\\d+)-(\\d{4})" )
  val matches = reg.matchEntire( "090-0001-2345" )
  matches?.groupValues?.forEach{ println( it ) }    // <== "090-0001-2345", "090", "0001", "2345"
  println( matches?.destructured?.component2())    // <== "0001"
  println( match?.groups?.get(2)?.value )    // <== "0001"
  println( match?.groups?.get(2)?.range )    // <== 4..7
  ```
* とりあえず簡単に分解してみる
  ```kotlin
  (Regex( "(\\d+)-(\\d+)-(\\d{4})" ).find("090-0001-2345")?.groups ?: listOf<MatchGroup>()).forEach{ println( it?.value ) }
  ```
  拡張ファンクション化
  ```kotlin
  import kotlin.text.*
  
  fun Regex.match( str: String ): Collection<MatchGroup?> = find( str )?.groups ?: listOf<MatchGroup>()
  
  fun main( args: Array<String> ) {
      Regex( "(\\d+)-(\\d+)-(\\d{4})" ).match( "090-0001-2345" ).forEach{ println( it?.value ) }
  }

  ```

## java.lang.String と kotlin.String の互換性
java.lang.String と kotlin.String は同じではない
```kotlin
val ks = "hoge"
val js:java.lang.String = ks
// ==> type mismatch: inferred type is kotlin.String but java.lang.String was expected
```
が、キャストすると代入できる
```kotlin
val ks = "hoge"
val js:java.lang.String = (ks as java.lang.String)
```
で、次のような Java のコードを Kotlin で実行しようとすると、エラーになる
```java
String s = new String( "abc".getBytes("ISO-8859_1"), "UTF-8" );
```
Kotlin の場合
```kotlin
val s = String( "abc".getBytes("ISO-8859_1"), "UTF-8" )
// ==> too many arguments for public constructor String() defined in kotlin.String
```
Kotlin には kotlin.String( byte[], String) というコンストラクターがない??  
そこで Kotlin では次のようにできる
```kotlin
import java.nio.charset.*

val s = "abc".toByteArray( Charset.forName( "ISO-8859-1" )).toString( Charset.forName( "UTF-8" ))
// または
val s = "abc".toByteArray( StandardCharsets.ISO_8859_1 ).toString( StandardCharsets.UTF_8 )
// Charset の渡し方を変えているだけで、やっていることは同じ
```

(参考)  
kotlin.String のコンストラクターから java.lang.String のコンストラクターを呼び出す処理は、kotlin-runtime-sources.jar の kotlin/text/StringsJVM.kt に記述されている

## その他
* 部分文字列  
Range を使う
  ```kotlin
  val hoge = "1234567890"
  hoge.substring( 3..5 )    // "456"
  ```
Java の substring( 3, 6 ) と同じ

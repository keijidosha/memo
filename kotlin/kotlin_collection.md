- Table of Content
{:toc}

Table of Contents
=================
* [xxxOf](#xxxof)
* [配列](#配列)
  * [int配列](#int配列)
* [リスト](#リスト)
* [map](#map)
* [Sequence](#sequence)
* [その他](#その他)

# xxxOf
| 関数名 | 生成されたインスタンス | メソッドシグネチャ | |
|-----|-----------------|------------|-|
| arrayListOf |ArrayList | fun <T> arrayListOf(vararg elements: T): ArrayList<T> ||
| arrayOf | パラメータに渡された型の配列 | |immutable |

|hashMapOf |HashMap |fun <K, V> hashMapOf(vararg pairs: Pair<K, V>): HashMap<K, V>||
|hashSetOf |HashSet |fun <T> hashSetOf(vararg elements: T): HashSet<T>||
|linkedMapOf |LinkedHashMap |fun <K, V> linkedMapOf(vararg pairs: Pair<K, V> ): LinkedHashMap<K, V>|順序が維持される|
|linkedSetOf |LinkedHashSet |fun <T> linkedSetOf(vararg elements: T): LinkedHashSet<T>|順序が維持される|
|listOf|Arrays| |immutable|
|mapOf |LinkedHashMap |fun <K, V> mapOf(vararg pairs: Pair<K, V>): Map<K, V>|immutable 順序が維持される|
|mutableListOf |ArrayList |fun <T> mutableListOf(vararg elements: T): MutableList<T>||
|mutableMapOf |LinkedHashMap |fun <K, V> mutableMapOf(vararg pairs: Pair<K, V> ): MutableMap<K, V>|順序が維持される|
|mutableSetOf |LinkedHashSet |fun <T> mutableSetOf(vararg elements: T): MutableSet<T>|順序が維持される|
|setOf |LinkedHashSet |fun <T> setOf(vararg elements: T): Set<T>, fun <T> setOf(): Set<T>, fun <T> setOf(element: T): Set<T>|immutable 順序が維持される|
|sortedMapOf |TreeMap |fun <K : Comparable<K>, V> sortedMapOf(vararg pairs: Pair<K, V> ): SortedMap<K, V>|初期化値がソートして格納される|
|sortedSetOf |TreeSet |fun <T> sortedSetOf(vararg elements: T): TreeSet<T>, fun <T> sortedSetOf(comparator: Comparator<in T>, vararg elements: T ): TreeSet<T> |初期化値がソートして格納される|	 	 	 

# 配列
ByteArray, ShortArray, IntArray, LongArray, FloatArray, DoubleArray, BooleanArary, CharArray
Javaで配列を引数とするメソッドを呼び出す場合、Kotlin側で配列を作成する必要がある  
(例) Java の場合

```java
char[] buf = new char[64];
int len;
while(( len = reader.read( buf, 0, buf.length )) >= 0 ) {
    // 処理
}
```
Kotlin の場合

```kotlin
val buf = CharArray( 64 )
var len = reader.read( buf, 0, buf.size )
while( len >= 0 ) {
    // 処理
    len = reader.read( buf, 0, buf.size )
}
```

* 整数の配列を要素数5個で作成する  
    `val iar = IntArray(5)`
* 配列要素の値を指定して生成する  
  `val iar = intArrayOf( 1, 2, 3, 4, 5 )`
  他にも shortArrayOf, byteArrayOf などプリミティブ型の配列を生成するファンクションが用意されている
* String の配列を要素数5個で作成する  
  null で初期化する  
  `val stringArray: Array<String?> = arrayOfNulls( 5 )`
* 指定した値で初期化する(String の配列を生成する場合など)  
  `val stringArray: = Array( 5, { idx -> (idx + idx).toString() })`  
  > [ "0", "2", "4", "8" ]
* 値を指定して初期化  
  `val stringArray = arrayOf( "その1", "その2", "その3", "その4", "その5" )`
* 作成した配列に要素の追加はできないが、値の変更は可能  
  `stringArray[0] = "No.1"`

## int配列
3通りの方法で int 配列を作成し、jad でどのような Javaソースが生成されているかを確認

Array<Int>
```
(kotlin)
val intArray = Array<Int>( 3, { idx -> idx + 1 } )
(java)
Integer intArray[] = new Integer[3];
// 以下代入式
```
IntArray
```
(kotlin)
val intArray = IntArray( 3, { idx -> idx + 1 } )
(java)
int intArray[] = new int[3];
// 以下代入式
```
arrayOf
```
(kotlin)
val intArray = arrayOf( 1, 2, 3 )
(java)
Integer intArray[] { Integer.valueOf(1), Integer.valueOf(2), Integer.valueOf(3) };
```
intArrayOf
```
(kotlin)
val intArray = intArrayOf( 1, 2, 3 )
(java)
int intArray[] { 1, 2, 3 };
```

IntArray と intArrayOf は int配列(プリミティブ)となり、以外は Integer配列(オブジェクト)となった。

# リスト
* listOf  
   `val list = listOf( 1, 2, 3 )`  
  java.util.Arrays が生成される  
  `list.add( 4 )    // エラー`
* arrayOf  
  `val list = arrayOf( 1, 2, 3 )`  
  Integer[3] の配列が生成される(リストではない)  
  (プリミティブ型の int ではなく、オブジェクトの Integer であることに注意。int配列を要求する Java のメソッドには渡せない)
  `list.add( 4 )    // もちろんエラー`
* arrayListOf  
  `val list = arrayListOf( 1, 2, 3 )`  
  ArrayList が生成される  
  `list.add( 4 )    // true( 4個目の要素が追加される)`
* MutableList(ArrayList)を要素がない状態で作成  
  `val list: MutableList<Int> = arrayListOf()`
* mutableMapOf  
  `val map = mutableMapOf<Int,Int>(Pair(1,2),Pair(3,4))`  
  // または  
  `val map = mutableMapOf<Int,Int>(1 to 2, 3 to 4)`  
  LinkedHashMap が生成される
  `map.put( 5, 6 )    // null(3個目のペアが追加される)`
* MutableMapを要素がない状態で作成  
  型を指定すれば、要素がない状態で初期リストを省略できる?  
  `val map: MutableMap<String,String> = mutableMapOf()`
* LinkedList は Java の API を呼び出す(linkedList(), linkedListOf() はなさそう?)  
    ```kotlin
    val list = java.util.LinkedList<String>()
    list.add( "Hello" )    // true
    ```

## reduce, fold を使ってたたみこむ(数値なら合計する)
* reduce  
  ```kotlin
  listOf( 1, 2, 3, 4, 5 ).reduce{ s, ii -> s + ii }
  ```
  1回目は s=1, ii=2(1個目と2個目の値), 2回目は s=3, ii=3(前回の合計値と 3個目の値)が渡される。
* fold  
  ```kotlin
  listOf( 1, 2, 3, 4, 5 ).fold( 0, { s, ii -> s + ii } )
  ```
  s の初期値に 0 を渡す。  
  1回目は s=0, ii=1(初期値と1個目の値), 2回目は s=1, ii=2(前回の合計値と 2個目の値)が渡される。

# map
* map を forEach でまわす  
  ```kotlin
  val map = mapOf(1 to "A", 2 to "B", 3 to "C")
  map.forEach {
      println( "${it.key} = ${it.value}" )
  }
  ```
* Kotlin 1.1 からの Destructuring in lambdas を使うと  
  ```kotlin
  val map = mapOf(1 to "A", 2 to "B", 3 to "C")
  map.forEach { (k, v) ->
      println( "${k} = ${v}" )
  }
  ```

# Sequence
Sequence に続くコレクション関数によっては 1件ずつ処理させる

(サンプル)

```kotlin
listOf( 1,2,3 ).filter {
    println( "filter: ${it}" )
    it % 2 == 0
}.forEach {
    println( "forEach: ${it}" )
}
```

これをコンパイルして実行すると、次のように filter 処理が終わってから forEach が実行される

> filter: 1  
> filter: 2  
> filter: 3  
> forEach: 2   

これを次のように asSequence を追加すると  
```kotlin
listOf( 1,2,3 ).asSequence().filter {
    println( "filter: ${it}" )
    it % 2 == 0
}.forEach {
    println( "forEach: ${it}" )
}
```
filter 処理と連動して forEach が実行される
> filter: 1  
> filter: 2  
> forEach: 2  
> filter: 3  
※asSequence() の後続のコレクション関数が sortedBy のように逐次処理できない場合は、sortedBy の処理が終わってから次の関数が実行される

# その他
* コレクションの一部の要素を削除  
  地道にイテレータで削除
  ```kotlin
  fun main( args: Array<String> ) {
  
      // hasNext(), next() でまわす
      val list = arrayListOf( 1, 2, 3, 4, 5 )
      val ite = list.iterator()
      while( ite.hasNext()) {
          val ii = ite.next()
          if( ii % 2 == 1 ) {
              ite.remove()
          }
      }
      println( list )
  
      // for でまわす
      val list2 = arrayListOf( 1, 2, 3, 4, 5 )
      val ite2 = list2.iterator()
      for( ii in ite2 ) {
          if( ii % 2 == 0 ) {
              ite2.remove()
          }
      }
      println( list2 )
  }
  ```

# Kotlin - Class
Table of Contents
=================

* [Table of Contents](#table-of-contents)
   * [クラスを扱う](#クラスを扱う)
      * [this](#this)
         * [Java](#java)
         * [Kotlin](#kotlin)
      * [class](#class)
         * [Java](#java-1)
         * [Kotlin](#kotlin-1)
      * [getClass()](#getclass)
         * [java](#java-2)
         * [Kotlin](#kotlin-2)
      * [型チェック](#型チェック)
         * [指定した型、またはその子孫のインスタンスかチェック(java の instanceof に相当)](#指定した型またはその子孫のインスタンスかチェックjava-の-instanceof-に相当)
         * [指定した型に完全に一致するかチェック(java の if( "hoge".getClass() == String.class ) に相当)](#指定した型に完全に一致するかチェックjava-の-if-hogegetclass--stringclass--に相当)
      * [コンストラクタ](#コンストラクタ)
         * [初期化処理](#初期化処理)
         * [セカンダリコンストラクタを宣言する](#セカンダリコンストラクタを宣言する)
         * [プライマリコンストラクタを private 宣言する(外からインスタンス化できなくする)](#プライマリコンストラクタを-private-宣言する外からインスタンス化できなくする)
         * [コンストラクタの引数にすべてデフォルト値が設定されている場合、引数なしのコンストラクタを自動的に生成する](#コンストラクタの引数にすべてデフォルト値が設定されている場合引数なしのコンストラクタを自動的に生成する)
         * [継承元のオーバーロードされた複数のコンストラクタごとに、継承先でコンストラクタを用意する場合](#継承元のオーバーロードされた複数のコンストラクタごとに継承先でコンストラクタを用意する場合)
      * [ネスト](#ネスト)
      * [enum](#enum)
      * [分解宣言](#分解宣言)
         * [dataクラスでないクラスで分解宣言を使う](#dataクラスでないクラスで分解宣言を使う)
      * [その他](#その他)
         * [ファクトリメソッドを実装してみる](#ファクトリメソッドを実装してみる)
         * [受け取った可変長引数を、別の可変長引数を要求するメソッドに渡す](#受け取った可変長引数を別の可変長引数を要求するメソッドに渡す)
         * [object宣言したクラス(シングルトン)への参照を、クラス名の文字列で取得する](#object宣言したクラスシングルトンへの参照をクラス名の文字列で取得する)
         * [シングルトン(object)で提供するメソッドをインターフェース化する](#シングルトンobjectで提供するメソッドをインターフェース化する)
         * [スレッド処理](#スレッド処理)


## クラスを扱う
### this
#### Java
```java
public class Hoge {
    public Hoge hoge2() {
        return this;
    }
}
```

#### Kotlin
「@Hoge」は省略可能。どういう場合に明示的に @Hoge を指定する必要があるのかはわからない。。
```kotlin
class Hoge {
    fun hoge2(): Hoge {
        return this@Hoge
    }
}
```

### class
#### Java
```java
ObjectMapper mapper = new ObjectMapper();
Map<String, String> result = mapper.readValue( jsonText, Map.class );
```
#### Kotlin
```kotlin
val mapper = ObjectMapper()
val result = mapper.readValue( jsonText, Map::class.java )
```

### getClass()
#### java
```java
InputStream is = getClass().getResourceAsStream( "/hoge.txt" );
```
#### Kotlin
```kotlin
InputStream is = this.javaClass.getResourceAsStream( "/hoge.txt" )
```

### 型チェック
#### 指定した型、またはその子孫のインスタンスかチェック(java の instanceof に相当)
```kotlin
if( "hoge" is String )
    println( "hoge is String" )
```

#### 指定した型に完全に一致するかチェック(java の if( "hoge".getClass() == String.class ) に相当)
```kotlin
if( "hoge"::class == String::class )
    println( "hoge is exactly String" )
```

### コンストラクタ
#### 初期化処理
```kotlin
class Person(val name: String) {
    val lengthOfName = name.length

    init {
        println( "initialized by name is $name" )
    }
}
```
初期処理は init に書く

#### セカンダリコンストラクタを宣言する
```kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```
※プライマリコンストラクタのパラメータ引数には val/var を指定できるが、セカンダリコンストラクタには指定できない。

#### プライマリコンストラクタを private 宣言する(外からインスタンス化できなくする)
プライマリコンストラクタであっても明示的に constructor を書くことで、private を指定できる。
```kotlin
class Hoge private constructor (val name: String) {}
```

#### コンストラクタの引数にすべてデフォルト値が設定されている場合、引数なしのコンストラクタを自動的に生成する
(例) 次のソースをコンパイル
```kotlin
class Customer(val customerName: String = ""){}
```
jad で逆コンパイルして生成された内容から抜粋
```java
public final class Customer {
    public final String getCustomerName() {
        return customerName;
    }

    public Customer(String customerName) {
        this.customerName = customerName;
    }

    public Customer(String s, int i, DefaultConstructorMarker defaultconstructormarker) {
        if((i & 1) != 0) s = "";
        this(s);
    }

    public Customer() {
        this(null, 1, null);
    }

    private final String customerName;
}
```

#### 継承元のオーバーロードされた複数のコンストラクタごとに、継承先でコンストラクタを用意する場合
プライマリコンストラクタは宣言せず、セカンダリコンストラクタをオーバーロードの数だけ宣言する。
```kotlin
class MyException: Exception {
    constructor( msg: String ): super( msg ){}
    constructor( msg: String, ex: Exception ): super( msg, ex ) {}
}
```
セカンダリコンストラクタからプライマリコンストラクタに引数を渡しつつ、継承元にも渡そうとしてうまくいかないため。
```kotlin
class MyException(msg: String): Exception(msg) {
    constructor(msg: String, ex: Exception): this(msg): super(msg, ex) {}    // コンパイルエラーになる
}
```

### ネスト
inner を付けると、内部のクラスから外側のクラスのフィールドを参照できる。
```kotlin
class hoge {
    private val name = "hoge"
    inner class fuga {
        fun say() {
            println(name)
        }
    }
}
```

### enum
enum を定義
```kotlin
enum class WeekDay { SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY }
```

値に応じて enum を取得、enum から値を取得
```kotlin
enum class WeekDay( val code: Int ) {
    SUNDAY(1), MONDAY(2), TUESDAY(3), WEDNESDAY(4), THURSDAY(5), FRIDAY(6), SATURDAY(7), UNKNOWN(-1);

    companion object {
        fun from( value: Int ): WeekDay = WeekDay.values().firstOrNull { it.code == value } ?: UNKNOWN
    }
}

val weekDay = WeekDay.from( 1 )
println( weekDay )         // ==> SUNDAY
println( weekDay.code )  // ==> 1
```

### 分解宣言
#### dataクラスでないクラスで分解宣言を使う
componentNメソッドを定義する
```kotlin
class Person( val id: Int, val name: String, val age: Int ) {
    operator fun component1(): Int = id
    operator fun component2(): String = name
    operator fun component3(): Int = age
}

fun main( args: Array<String> ) {
    val p1 = Person( 1, "hoge", 15 )
    val p2 = Person( 2, "fuga", 20 )

    val (id1, name1, age1 ) = p1
    println( "id = $id1, name = $name1, age = $age1" )

    val (id2, name2, age2 ) = p2
    println( "id = $id2, name = $name2, age = $age2" )
}
```
出力結果
```
id = 1, name = hoge, age = 15
id = 2, name = fuga, age = 20
```

### その他
#### ファクトリメソッドを実装してみる
Hoge.kt
```kotlin
data class Hoge( val id:Int, val name: String ) {
    companion object {
        fun newHoge( parameter: String ): Hoge {
            val id = parameter.substring( 0..1 ).toInt()
            val name = parameter.substring( 2..parameter.length-1 )
            return Hoge( id, name )
        }
    }
}

Hoge.newHoge( "11taro" )    // Hoge(id=11, name=taro)
```

#### 受け取った可変長引数を、別の可変長引数を要求するメソッドに渡す
可変長引数を別のメソッドに渡す時、引数の前に * を付ける。
```kotlin
fun main( args: Array<String> ) {
    hoge1( "hello", "Kyoto", "Osaka", "Kobe" )
}

fun hoge1( msg: String, vararg param: String ) {
    println( msg )
    hoge2( *param )
}

fun hoge2( vararg param: String ) {
    param.forEach {
        println( it )
    }
}
```

#### object宣言したクラス(シングルトン)への参照を、クラス名の文字列で取得する
```kotlin
interface Hoge {
    fun hoge()
}

object Hoge2: Hoge {
    override fun hoge() { println( "hoge2" ) }
}
...
val klass: Hoge = Class.forName( "Hoge2" ).kotlin.objectInstance as? Hoge ?: Hoge2
```
kotlin-reflect.jar が必要。  
[https://stackoverflow.com/questions/47675033/how-do-i-use-kotlin-object-by-reflection](https://stackoverflow.com/questions/47675033/how-do-i-use-kotlin-object-by-reflection)

#### companion object を拡張する
```
class MyClass {
    companion object { } // will be called "Companion"
}

fun MyClass.Companion.foo() { ... }

MyClass.foo()
```

#### シングルトン(object)で提供するメソッドをインターフェース化する
```
fun main(args: Array<String>) {
    var hoge: Hoge = Hoge2
    hoge.greet("Tom")
    hoge = Hoge3
    hoge.greet("Mike")
    hoge = Hoge4
    hoge.greet("Jimmy")
    hoge = Hoge4()
    hoge.greet("Chris")
}

interface Hoge {
    fun greet(name: String)
}

object Hoge2: Hoge {
    override fun greet(name: String) { println( "Welcome $name from Hoge2" )}
}

object Hoge3: Hoge {
    override fun greet(name: String) { println( "Welcome $name from Hoge3" )}
}

class Hoge4: Hoge {
    companion object: Hoge {
        override fun greet(name: String) { println( "Welcome $name from Hoge4" )}
    }
    override fun greet(name: String) { println( "Welcome $name from Hoge4.Instance" )}
}
```

#### スレッド処理
* kotlin.concurrent.thread メソッドを使う  
  ```kotlin
  kotlin.concurrent.thread{ println( "Hello" ) }
  ```
* SAMを利用して Thread#run() を実装  
  ```kotlin
  Thread{ println( "hello" ) }.start()
  ```
* Runnable を実装した無名クラスを作成  
  ```kotlin
  Thread( object: Runnable {
      override fun run() {
          println( "hello" )
      }
  }).start()
  ```
* Runnable を実装したクラスを作成  
  ```kotlin
  fun main( args: Array<String> ) {
      val runner = RunnableImpl()
      val thread = Thread( runner )
      thread.start()
  }
  
  class RunnableImpl : Runnable
  {
      override fun run() {
          println( "hello" )
      }
  }
  ```


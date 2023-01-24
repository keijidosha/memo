## 定数
```
class Hoge {
    companion object {
        const val LENGTH = 16
        const val NAME = "Hoge"
    }

    fun showName() {
        println( NAME )
    }
}
```
※const は primitive型、または String型に対してだけ使える。String 以外のクラスに使うとコンパイルエラー。

## nullable
### プリミティブ型
nullable なプリミティブ型は、オブジエクトにボクシングされる。  
ただし、null が代入されないことが明らかな場合はプリミティブ型のままで扱われる。  
(例) null が代入されることのない num と、代入される nullableNum  
```
var num: Int? = 0
var nullableNum: Int? = 1
nullableNum = null
```

このコードを jad すると次のような感じになる  
```
int num = 0;
Integer nullableNum = Interger.valueOf( 1 );
nullableNum = (Integer)null;
```

## キャスト
as を使う

```
val url = URL( "http://www.yahoo.co.jp/" )
val conn = url.openConnection() as HttpURLConnection
```

Long から Int へのキャストはできない  
```
val ll: Long = 3L
val ii: Int = ll as Int  // <== エラー
```

toInt() を使う  
```
val ii: Int = ll.toInt()
```

## プロパティのオーバーライド
* interface で定義された val プロパティを var でオーバーライドできる  
  ```
  interface Foo { val count: Int }                  // val でプロパティを定義
  class Bar1( override val count: Int ) : Foo       // プライマリコンストラクタの引数としてオーバーライド
  class Bar2 : Foo { override var count: Int = 0 }  // var でオーバーライド
  ```

## 遅延初期化
### lazy: val(イミュータブルな)変数を遅延初期化する

val は変数の宣言とともに初期化する必要がある  
```
val str = "Hoge"
```

lazy を使うと遅延初期化できる  
```
fun main( args: Array<String> ) {
    val v = ValTest1()
    println( "---------" )
    println( v.hoge )
}

class ValTest1 {
    val hoge: String by lazy { getHogeString() }
    init {
        println( ">>> init" )
    }

    fun getHogeString(): String {
        println( ">>> getHogeString" )
        return "Hoge"
    }
}
```
v.hoge が参照された時点で初期化される

staticなオブジェクトのイミュータブルな変数(val)を遅延初期化するサンプル  
```
import java.io.*
import java.util.*

object Hoge
{
    val prop = Properties()
    val hogeValue: String by lazy { prop.getProperty( "hoge" ) }

    init {
            this.javaClass.getResourceAsStream( "config.properties" ).reader().use() {
            prop.load( it )
        }
    }
}

fun main( args: Array<String> ) {
    println( Hoge.hogeValue )
}
```
※コンパイルすると、"by lazy" の定義ごとに匿名クラスが作成される。

### lateinit: var(ミュータブルな)変数を遅延初期化する
lateinit を使う  
※val に lateinit は使えない。by lazy を使う。  
※Int などプリミティブ型に lateinit は使えない。  
```
import kotlin.properties.Delegates

class Hoge {
    var fuga1 : String by Delegates.notNull()
    lateinit var fuga2 : String
}

fun main(args : Array<String>) {
    val hoge1 = Hoge()
    hoge1.fuga1 = "fuga1"
    hoge1.fuga2 = "fuga2"
    println(hoge1.fuga1)
    println(hoge1.fuga2)

    val hoge2 = Hoge()
    hoge2.fuga1 = "fuga1"
    hoge2.fuga2 = "fuga2"
    println(hoge2.fuga1.toString())
    println(hoge2.fuga2.toString())

    val hoge3 = Hoge()
    hoge3.fuga1 = "fuga1"
    hoge3.fuga2 = "fuga2"
    println(hoge3.fuga1.toLowerCase())
    println(hoge3.fuga2.toLowerCase())

    val hoge4 = Hoge()
    hoge4.fuga1 = "fuga1"
    hoge4.fuga2 = "fuga2"
    println(hoge4.fuga1.substring(2))
    println(hoge4.fuga2.substring(2))
}
```

isInitialized で初期化済みがどうか判定可能(1.2 から)  
```
class LateInit {
    lateinit var fuga2 : String

    fun run() {
        if(::fuga2.isInitialized)
            println( fuga2 )
        else
            println( "fuga2 not initizliaed" )
    
        fuga2 = "fuga2"
        println( fuga2 )
    }
}

fun main( args: Array<String> ) {
    LateInit().run()
}
```


内部では Delegates.notNull() が使われている?  
jad で逆コンパイルすると、同じ Javaソースが生成された  
同じではないという情報もあり([Kotlin : 'notNull delegate' vs 'lateinit'](https://qiita.com/Reyurnible/items/95660497a172a177f08f))  
```
hoge4 = new Hoge();
hoge4.setFuga1("fuga1");
hoge4.setFuga2("fuga2");
String s = hoge4.getFuga1();
byte byte0 = 2;
if(s == null)
    throw new TypeCastException("null cannot be cast to non-null type java.lang.String");
String s3 = ((String)s).substring(byte0);
Intrinsics.checkExpressionValueIsNotNull(s3, "(this as java.lang.String).substring(startIndex)");
s = s3;
System.out.println(s);
s = hoge4.getFuga2();
byte0 = 2;
if(s == null)
    throw new TypeCastException("null cannot be cast to non-null type java.lang.String");
String s4 = ((String)s).substring(byte0);
Intrinsics.checkExpressionValueIsNotNull(s4, "(this as java.lang.String).substring(startIndex)");
s = s4;
System.out.println(s);
```

初期化前にプロパティの値を参照すると、IllegalStateException がスローされる  
```
public fun <T: Any> notNull(): ReadWriteProperty<Any?, T> = NotNullVar()

private class NotNullVar<T: Any>() : ReadWriteProperty<Any?, T> {
    private var value: T? = null

    public override fun getValue(thisRef: Any?, property: KProperty<*>): T {
        return value ?: throw IllegalStateException("Property ${property.name} should be initialized before get.")
    }

    public override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
        this.value = value
    }
}
```

## クラスの型を使う
Java で使っていた <クラス名>.class を Kotlin で使う  
Java では次のようにしていたのを  
```
ObjectMapper mapper = new ObjectMapper();
Map<String, String> result = mapper.readValue( jsonText, Map.class );
```
Kotlin の場合は次のようにする  
```
val mapper = ObjectMapper()
val result = mapper.readValue( jsonText, Map::class.java )
```

## その他
### 変数の型を確認

javaClass を使用  
```
val str = "hoge"
println( str.javaClass.name )
```

### volatile
kotlin で volatile な変数を宣言する  
```
class Hoge
{
    @Volatile var hoge = false
}
```

次のような Javaクラスが生成される  
```
public final class Hoge {
    private volatile boolean hoge;

    public Hoge() {}

    public final boolean getHoge() {
        return hoge;
    }

    public final void setHoge(boolean val) {
        hoge = val;
    }
}
```

### 変数の値がクロージャ内で変更されると、スマートキャストできない
次のソースがコンパイルエラーになる  
smart cast to 'String' is impossible, because 'str' is a local variable that is captured by a changing closure  
```
fun hoge(): String {
    var str: String = null
    listOf( "abc", null, "def" ).forEach {
        if( it != null )
            str = it
    }

    if( str != null )
        return str
    else
        return ""
}
```

エルビス式を使って解決  
```
    return str ?: ""
```

# Kotlin: 1.2

## vararg
vararg にひとつだけのパラメータを渡す場合、次のように書くと 1.2 から警告が出力されるようになる。
(1.3 からはコンパイルエラーになる??)
```kotlin
val s = "abc,def"
println( s.split(delimiters = ','))
```
warning: assigning single elements to varargs in named form is deprecated

What's New in Kotlin 1.2: [Deprecation: single named argument for vararg](https://kotlinlang.org/docs/reference/whatsnew12.html#deprecation-single-named-argument-for-vararg)

対策: 次のように配列にし、* を付けて渡す。
```kotlin
val s = "abc,def"
println( s.split(delimiter = *charArrayOf(',')))
```

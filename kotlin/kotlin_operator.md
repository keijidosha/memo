# Kotlin: 演算子

## 比較演算子

* ==: 値の比較  
Javaの equals() が呼ばれる  
str1 = "hoge" + "fuga"  
str2 = "hog" + "efuga"  
println( str1 == str2 )    // true  
* ===: オブジェクト参照の比較  
str1 = "hoge" + "fuga"  
str2 = "hog" + "efuga"  
str3 = str1  
println( str1 === str2 )    // false  
println( str1 === str3 )    // true  
* in: 範囲  
println( 3 in 1..5 )    // true  
in の否定は !in(! と in の間にスペースを入れない)  
println( 0 !in 1..5 )    // true  


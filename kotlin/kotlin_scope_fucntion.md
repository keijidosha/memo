## 概要

|関数名|渡し方|参照|戻り値|例(val str = "hoge")|
|---|---|---|---|---|
|apply|関数|this|this|val strApply = str?.apply{ println( toUpperCase()) }|
|run|関数|this|結果の値|val upperStr = str?.run{ toUppserCase() }|
|let|関数|it|結果の値|val upperStr = str?.let{ **it**.toUppserCase() }|
|also|関数|it|this|val strAlso = str?.also{ println( **it**.toUpperCase()) }|
|with|引数|this|結果の値|val upperStr = with(**str**){ toUpperCase() }|

## apply
```
val listApply = mutableListOf<String>().apply {
 add("hoge")
 add("fuga")
}
println( "apply(1): $listApply" )
```
> apply(1): [hoge, fuga]

## with
```
val listWith = mutableListOf<String>()
with( listWith ) {
 add("hoge")
 add("fuga")
}
println( "with(1): $listWith" )
```
> with(1): [hoge, fuga]

## let
```
val listLet = mutableListOf<String>()
val ret = listLet.let {
 it.add( "hoge" )
 it.add( "fuga" )
}
println( "let(1): $listLet" )
println( "let(1): $ret" )
```
> let(1): [hoge, fuga]  
> let(1): true
```
val listLet2 = mutableListOf<String>().let {
 it.add( "hoge" )
 it.add( "fuga" )
 it
}
println( "let(2): $listLet2" )
```
> let(2): [hoge, fuga]

null でない場合だけ実行する
```
val listLet3: MutableList<String>? = null
listLet3?.let {
 it.add( "hoge" )
 it.add( "fuga" )
}
println( "let(3): $listLet3" )
```
> let(3): null

## run
```
val listRun = mutableListOf<String>()
val ret2 = listRun.run {
 add("hoge")
 add("fuga")
}
println( "run(1): $listRun" )
println( "run(1): $ret2" )
```
> run(1): [hoge, fuga]  
> run(1): true
```
val listRun2 = mutableListOf<String>().run {
 add("hoge")
 add("fuga")
 this
}
println( "run(2): $listRun2" )
```
> run(2): [hoge, fuga]

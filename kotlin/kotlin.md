# Kotlin

* ソースダウンロード  
https://github.com/JetBrains/kotlin/tree/1.0.3/libraries/stdlib/src/kotlin


## コンパイルと実行

* Hoge.kt
  ```
  fun main( args: Array<String> )
  {
      println( "hogeee" )
  }
  ```
* コンパイル  
`kotlinc Hoge.kt`
* 実行 クラス名に Kt を付けて実行する  
  `kotlin HogeKt`  
  または  
  `java -cp .:$KOTLIN_HOME/lib/kotlin-runtime.jar HogeKt`  
  Kotlin は fun main(args: Array<String>) があると、<クラス名>Kt.class に publc static void main( String[] args) メソッドを生成するため
* コンパイルしてランタイム付きの JARファイルを作成  
`kotlinc -d hoge.jar -include-runtime Hoge.kt`
* 実行  
`java -jar hoge.jar`

* コンパイル時に未使用パラメータの警告を出力しないようにする  
=> parameter 'xxx' is never used  
@Suppress("UNUSED_PARAMETER") を指定する  
(例) パラメータに対して指定する  
  ```
  fun hoge( @Suppress("UNUSED_PARAMETER") hoge: String ) {
  }
  ```
  (例)  メソッドに対して指定する  
  ```
  @Suppress("UNUSED_PARAMETER")
  fun hoge(  hoge: String ) {
  }
  ```
  他に指定できそうなパラメータ  
  UNCHECKED_CAST, DEPRECATION, REDUNDANT_NULLABLE, UNNECESSARY_NOT_NULL_ASSERTION
* 環境変数 CLASSPATH を参照してコンパイル、実行するように kotlinc スクリプトを編集  
bin/kotlinc スクリプトの末尾を次のように編集する  
  ```
  if [ -n "$CLASSPATH" ]; then
      "${JAVACMD:=java}" $JAVA_OPTS "${java_args[@]}" -cp "${kotlin_app[@]}" -cp $CLASSPATH "${kotlin_args[@]}"
  else
      "${JAVACMD:=java}" $JAVA_OPTS "${java_args[@]}" -cp "${kotlin_app[@]}" "${kotlin_args[@]}"
  fi
  ```
  kotlin(実行用スクリプト)は環境変数 KOTLIN_RUNNER に 1をセットして kotlinc を呼び出しているだけので、kotlinc を編集すると kotlin も $CLASSPATH を参照してくれるようになる。
* java.util.ServiceLoader を使った処理が、kotlin では動的にクラスが読み込まれない  
JavaVM を起動する時、kotlin コマンドではなく java コマンドを使う。(kotlin-runner.jar に CLASSPATH を通しておく)  
kotlin コマンドを使うと、org.jetbrains.kotlin.runner.Main クラス経由で実行されるためか、ServiceLoader がうまく機能しない模様

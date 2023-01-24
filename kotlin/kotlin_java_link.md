# Java連携

## Log4j

* Log4j2 にラムダ式を渡し、ログ出力する場合だけ遅延実行する  
org.apache.logging.log4j.util.Supplier を使う(java.util.function.Supplier ではなく)  
  ```
  import java.util.UUID
  import org.apache.logging.log4j.Logger
  import org.apache.logging.log4j.LogManager
  import org.apache.logging.log4j.util.Supplier
  
  fun main( args: Array<String> ) {
      val logger = LogManager.getLogger()
  
      logger.info("info {}", Supplier { hoge() } )
  }
  
  fun hoge(): String {
      println( "exec hoge" )
      return UUID.randomUUID().toString()
  }
  ```
  Java8 でラムダ式がサポートされたため、Supplier は Log4j2 3.0 で削除されるらしい。  
  Log4j2 2.8 では java.util.function.Supplier を Logger に渡すことはできない模様。(単に Supplier.toString() が出力されてしまうだけ)  
  Log4j2 3.0 では java.util.function.Supplier が渡せるようになる??  

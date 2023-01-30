# Kotlin - 並列処理


## スレッド処理

* kotlin.concurrent.thread メソッドを使う  
`kotlin.concurrent.thread{ println( "Hello" ) }`
* SAMを利用して Thread#run() を実装  
`Thread{ println( "hello" ) }.start()`
* Runnable を実装した無名クラスを作成  
  ```
  Thread( object: Runnable {
      override fun run() {
          println( "hello" )
      }
  }).start()
  ```
* Runnable を実装したクラスを作成  
  ```
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

## 排他ロック

* synchronized  
kotlin でも synchronized は使える  
メソッドに対して排他制御する場合は @Synchronized  
  ```
  import kotlin.concurrent.*
  
  fun main( args: Array<String> ) {
      Synchronized2().exec()
  }
  
  class Synchronized2 {
      fun exec() {
          val lock = Any()
  
          repeat( 10 ) {
              thread{ run( lock ) }
          }
          repeat( 10 ) {
              Thread{ run2( it ) }.start()
          }
      }
  
      private fun run( lock: Any ) {
          synchronized( lock ) {
              print( '<')
              Thread.sleep( 500 )
              print( '>')
          }
      }
  
      @Synchronized run2( id: Int ) {
              print( "<(${id})")
          Thread.sleep( 500 )
          print( '>')
      }
  }
  ```
* withLock(拡張関数)  
kotlin の拡張関数 withLock を使う
  ```
  import java.util.concurrent.locks.*
  import kotlin.concurrent.*
  
  fun main( args: Array<String> ) {
      Lock2().exec()
  }
  
  class Lock2 {
      fun exec() {
          val lock = ReentrantLock()
  
          repeat( 10 ) {
              thread{ run( lock ) }
          }
      }
  
      private fun run( lock: Lock ) {
          lock.withLock {
              print( '<' )
              Thread.sleep( 500 )
              print( '>' )
          }
      }
  }
  ```
* ReadWriteLock(拡張関数)  
readロック中に read は実行できる(ロックされない)が write は実行できない(ロックされる)  
writeロック中は read も write も実行できない  
  ```
  import java.util.*
  import java.util.concurrent.locks.*
  import kotlin.concurrent.*
  
  fun main( args: Array<String> ) {
      val lock = ReentrantReadWriteLock()
  
      val task1 = ReadWriteLock2( "task1", lock )
      val task2 = ReadWriteLock2( "task2", lock )
      val task3 = ReadWriteLock2( "task3", lock )
  
      thread{ task1.read() }
      thread{ task2.read() }
      thread{ task3.write() }
      thread{ task1.read() }
      thread{ task2.write() }
      thread{ task3.write() }
  }
  
  class ReadWriteLock2( val name: String, val lock: ReentrantReadWriteLock ) {
  
      fun read() {
          lock.read {
              println( "$name: read -->" )
              Thread.sleep( 500 )
              println( "$name: read <--" )
          }
      }
  
      fun write() {
          lock.write {
              println( "$name: write -->" )
              Thread.sleep( 500 )
              println( "$name: write <--" )
          }
      }
  }
  ```  
  (結果)  
  ```
  task1: read -->
  task2: read -->
  task2: read <--
  task1: read <--
  task3: write -->
  task3: write <--
  task1: read -->
  task1: read <--
  task2: write -->
  task2: write <--
  task3: write -->
  task3: write <--
  ```

## タイマー

* 初回待ち時間と繰り返し間隔を指定して実行
```
import kotlin.concurrent.*

fun main( args: Array<String> ) {
   var cnt = 0

  val timer2  = timer( daemon = true, initialDelay = 1000, period = 500 ) {
    println( "Hello" )
    cnt += 1
    if( cnt == 5 ) {
      cancel()
    }
  }

  for( ii in 1..100 ) {
    Thread.sleep( 100 )
    if( cnt == 5 ) {
      timer2.cancel()
      break
    }
  }

}
```



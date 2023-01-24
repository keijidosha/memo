# Kotlin: 末尾再帰

* (例1) 1から指定された数までの合計を求める
  ```
  fun main( args: Array<String> ) {
      val tr = TailRecursive2()
  
      println( "10: " + tr.sum1( 10 ))    // 55
      println( "20: " + tr.sum2( 20 ))    // 210
  
  }
  
  
  class TailRecursive2 {
  
      tailrec fun sum1( n: Int, sum: Int = 0 ): Int {
          return when( n ) {
   1 -> sum + n
              else -> {
                  sum1( n - 1, sum + n )
              }
          }
      }
  
  
      // sum1 の 1行版
      tailrec fun sum2( n: Int, sum: Int = 0 ): Int = if( n == 1 ) sum + 1 else sum2( n - 1, sum + n )
  }
  ```
* (例2) リストの要素を末尾再帰で足しこんでいく
  ```
  fun main( args: Array<String> ) {
      val tr = TailRecursive3()
  
      println( tr.sum1( listOf( 1, 2, 3, 4, 5, 6, 7, 8, 9, 10)))    // 55
      println( tr.sum2( listOf( 1, 2, 3, 4, 5, 6, 7, 8, 9, 10)))    // 55
  }
  
  class TailRecursive3 {
      tailrec fun sum1( list: List<Int>, sum: Int = 0 ) : Int {
          return when( list.size ) {
              0 -> sum
              else -> sum1( list.drop( 1 ), sum + list.first())
          }
      }
  
      tailrec fun sum2( list: List<Int>, sum: Int = 0 ) : Int = if( list.size == 0 ) sum else sum2( list.drop( 1 ), sum + list.first())
  }
  ```  
  本当は次のようにしたいが、末尾に計算式が入っていると、whileループに最適化されない(再帰の回数が多くなるとスタックオーバーフローする)
  ```
  fun main( args: Array<String> ) {
      val tr = TailRecursive1()
  
      println( "10: " + tr.sum1( 10 ))
      println( "20: " + tr.sum2( 20 ))
  }
  
  class TailRecursive1 {
      tailrec fun sum1( n: Int ): Int {
          return when( n ) {
              1 -> n
              else -> {
                  n + sum1( n -1 )
              }
          }
      }
  
      tailrec fun sum2( n: Int ): Int = if( n == 1 ) n else n + sum2( n - 1 )
  }
  ```


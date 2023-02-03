# Kotlin: Coroutines

Table of Contents
-----------------

* [launch](#launch)
* [cancel and join](#cancel-and-join)
   * [中断関数を実行していないのでキャンセルできない](#中断関数を実行していないのでキャンセルできない)
   * [isActive をチェックする](#isactive-をチェックする)
   * [delay() を実行する](#delay-を実行する)
   * [yield() を実行する](#yield-を実行する)
   * [タイムアウトを設定する](#タイムアウトを設定する)
* [async/await](#asyncawait)
* [Channel](#channel)
   * [チャネルバッファ](#チャネルバッファ)
   * [パイプライン](#パイプライン)
   * [パイプラインその2](#パイプラインその2)
   * [Channel から複数の consumer coroutine で受信して処理](#channel-から複数の-consumer-coroutine-で受信して処理)
   * [Channel に対して複数の coroutine から送信](#channel-に対して複数の-coroutine-から送信)
   * [レスポンス/双方向チャネル](#レスポンス双方向チャネル)
   * [チャネル読み込みタイムアウト](#チャネル読み込みタイムアウト)
* [produce](#produce)
* [actor](#actor)
* [actor その2](#actor-その2)
* [select](#select)
   * [receive](#receive)
   * [send](#send)
   * [close](#close)
   * [delay](#delay)
* [その他](#その他)
   * [処理中に発生した例外により、チャネルに残ったデータを別のコルーチンが処理する](#処理中に発生した例外によりチャネルに残ったデータを別のコルーチンが処理する)
   * [Actor が落ちたら、新たに Actor を起動して継続](#actor-が落ちたら新たに-actor-を起動して継続)
   * [Channel(actor) に CompleteDeffered を渡し、結果を受け取るまで待機する](#channelactor-に-completedeffered-を渡し結果を受け取るまで待機する)
   * [CompleteDeffered を使いまわして再利用する](#completedeffered-を使いまわして再利用する)
   * [Channel に xxx を渡し、処理が終わると xxx が走る](#channel-に-xxx-を渡し処理が終わると-xxx-が走る)
   * [Coroutines を使ってテキストファイルを非同期読み込みする](#coroutines-を使ってテキストファイルを非同期読み込みする)

### launch
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay

suspend fun doWorld() {
    delay( 1000L )
    println( "world" )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val job = launch { doWorld() }
    println( "hello " )
    job.join()
}
```

### cancel and join
#### 中断関数を実行していないのでキャンセルできない
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.cancelAndJoin

fun main( args: Array<String> ) = runBlocking<Unit> {
    val startTime = System.currentTimeMillis()
    val job = launch {
        var nextPrintTime = startTime
        var i = 0

        while( i < 10 ) {
            if( System.currentTimeMillis() > nextPrintTime ) {
                println( "I'm running ${i++} ..." )
                nextPrintTime += 500L
            }
        }
    }

    delay( 1300L )
    println( "main: I'm tired of waiting" )
    job.cancelAndJoin()
    println( "main: Now I can quit" )
}
```

#### isActive をチェックする
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.cancelAndJoin

fun main( args: Array<String> ) = runBlocking<Unit> {
    val startTime = System.currentTimeMillis()
    val job = launch {
        var nextPrintTime = startTime
        var i = 0

        while( isActive ) {
            if( System.currentTimeMillis() > nextPrintTime ) {
                println( "I'm running ${i++} ..." )
                nextPrintTime += 500L
            }
        }
    }

    delay( 1300L )
    println( "main: I'm tired of waiting" )
    job.cancelAndJoin()
    println( "main: Now I can quit" )
}
```

#### delay() を実行する
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.cancelAndJoin

fun main( args: Array<String> ) = runBlocking<Unit> {
    val startTime = System.currentTimeMillis()
    val job = launch {
        var nextPrintTime = startTime
        var i = 0

        while( i < 10 ) {
            if( System.currentTimeMillis() > nextPrintTime ) {
                println( "I'm running ${i++} ..." )
                nextPrintTime += 500L
            }
            delay( 1 )
        }
    }

    delay( 1300L )
    println( "main: I'm tired of waiting" )
    job.cancelAndJoin()
    println( "main: Now I can quit" )
}
```

#### yield() を実行する
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.yield
import kotlinx.coroutines.experimental.cancelAndJoin
import kotlinx.coroutines.experimental.CancellationException

fun main( args: Array<String> ) = runBlocking<Unit> {
    val startTime = System.currentTimeMillis()
    val job = launch {
        var nextPrintTime = startTime
        var i = 0

        try {
            while( i < 10 ) {
                if( System.currentTimeMillis() > nextPrintTime ) {
                    println( "I'm running ${i++} ..." )
                    nextPrintTime += 500L
                }
                yield()
            }
        } catch( ex: CancellationException ) {
            println( "job was cancelled" )
        } finally {
            println( "finally" )
        }
    }

    delay( 1300L )
    println( "main: I'm tired of waiting" )
    job.cancelAndJoin()
    println( "main: Now I can quit" )
}
```

キャンセルされると CancellationException がスローされる。  
try - catch - finally でキャンセル時の処理を記述できる。

#### タイムアウトを設定する
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.withTimeout
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.yield
import kotlinx.coroutines.experimental.cancelAndJoin
import kotlinx.coroutines.experimental.CancellationException

fun main( args: Array<String> ) = runBlocking<Unit> {
    val startTime = System.currentTimeMillis()
    val job = launch {
        withTimeout( 1500L ) {
            var nextPrintTime = startTime
            var i = 0

            try {
                while( i < 10 ) {
                    if( System.currentTimeMillis() > nextPrintTime ) {
                        println( "*** I'm running ${i++} ..." )
                        nextPrintTime += 500L
                    }
                    yield()
                }
            } catch( ex: CancellationException ) {
                println( "*** job was cancelled" )
            } finally {
                println( "*** finally" )
            }
        }
    }

    delay( 300L )
    println( "main: I'm tired of waiting" )
    job.join()
    println( "main: Now I can quit" )
}
```

タイムアウトを設定した場合も、yield() などの中断関数を入れておかないとタイムアウトしない。

### async/await
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.async
import kotlin.system.measureTimeMillis

suspend fun doubler( num: Int ) = async {
    num + num
}

suspend fun squre( num: Int ) = async {
    num * num
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = doubler( 10 )
        val two = squre( 10 )
        println( "The answer is ${one.await() + two.await()}" )
    }

    println( "Completed in $time ms" )
}
```

次のように async ブロック内で呼び出してもよい。(が、生成されるコードは異なるような気がする。。)

```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.async
import kotlin.system.measureTimeMillis

suspend fun doubler( num: Int ) = num + num

suspend fun squre( num: Int ) = num * num

fun main( args: Array<String> ) = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async { doubler( 10 ) }
        val two = async { squre( 10 ) }
        println( "The answer is ${one.await() + two.await()}" )
    }

    println( "Completed in $time ms" )
}
```

### Channel
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.channels.Channel

fun main( args: Array<String> ) = runBlocking<Unit> {
    val channel = Channel<Int>()
    launch {
        for( ii in 1..5 ) channel.send( ii + ii )
        channel.close()
    }
    for( num in channel ) { println( num ) }
    println( "Done" )
}
```
Channel に対して send でデータを送信し、for で受信。  
Channel を close すると for を抜ける。

#### チャネルバッファ
Channel のコンストラクタにバッファサイズを指定  
送信側はバッファサイズまでメッセージを溜めることができる(受信側がメッセージを消化しなくても)
```kotlin
val channel = Channel<Int>(3)
```

#### パイプライン
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.channels.produce
import kotlinx.coroutines.experimental.channels.ReceiveChannel

fun produceNumbers() = produce<Int> {
    var x = 1
    while( true ) send( x++ )
}

fun produceDouble( numbers: ReceiveChannel<Int> ) = produce<Int> {
    for( x in numbers ) send( x + x )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val numbers = produceNumbers()
    val squares = produceDouble( numbers )
    for( i in 1..5 ) { println( squares.receive()) }
    println( "Done" )
    squares.cancel()
    numbers.cancel()
}
```

#### パイプラインその2
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.channels.Channel
import kotlinx.coroutines.experimental.channels.SendChannel
import kotlinx.coroutines.experimental.channels.ReceiveChannel

suspend fun doubler( chData: ReceiveChannel<Int>, chRes: SendChannel<Int> ) {
   for( num in chData ) {
        chRes.send( num + num )
    }
    println( "doubler end" )
}

suspend fun sender( ch: Channel<Int> ) {
    var ii = 1

    while( true ) {
        ch.send( ii++ )
    }
}

suspend fun receiver( ch: Channel<Int> ) {
    for( num in ch ) {
        delay( 100 )
        println( num )
    }
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val chDouble = Channel<Int>()
    val chResult = Channel<Int>()
    val jobRec    = launch { receiver( chResult ) }
    val jobDouble = launch { doubler( chDouble, chResult ) }
    val jobSend   = launch { sender( chDouble ) }

    delay( 1000 )
    jobSend.cancel()
    //jobDouble.cancel()
    //jobRec.cancel()
    jobSend.join()

    chResult.close()
    chDouble.close()

    jobDouble.join()
    jobRec.join()
}
```
Channel は SendChannel, ReceiveChannel を拡張している。  
Channel を SendChannel, ReceiveChannel として渡すことで送信専用、受信専用チャネルとして役割を明確にできる。

#### Channel から複数の consumer coroutine で受信して処理
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.channels.produce
import kotlinx.coroutines.experimental.channels.ReceiveChannel
import kotlinx.coroutines.experimental.channels.consumeEach

fun produceNumbers() = produce<Int> {
    var x = 1
    while( true ) {
        send( x++ )
        delay( 100 )
    }
}

fun launchConsumer( id: Int, channel: ReceiveChannel<Int> ) = launch {
    channel.consumeEach {
        println( "Consumer #$id received $it" )
    }
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val producer = produceNumbers()
    repeat( 5 ) { launchConsumer( it, producer ) }
    delay( 2000 )
    producer.cancel()
}
```

#### Channel に対して複数の coroutine から送信
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.cancelChildren
import kotlinx.coroutines.experimental.channels.Channel
import kotlinx.coroutines.experimental.channels.SendChannel
import kotlin.coroutines.experimental.coroutineContext

suspend fun sendString( channel: SendChannel<String>, s: String, time: Long ) {
    while( true ) {
        delay( time )
        channel.send( s )
    }
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val channel = Channel<String>()
    launch( coroutineContext ) { sendString( channel, "foo", 200L ) }
    launch( coroutineContext ) { sendString( channel, "bar", 500L ) }
    repeat( 6 ) {
        println( channel.receive())
    }
    coroutineContext.cancelChildren()
}
```

#### レスポンス/双方向チャネル
#### チャネル読み込みタイムアウト
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.withTimeout
import kotlinx.coroutines.experimental.TimeoutCancellationException
import kotlinx.coroutines.experimental.channels.Channel
import kotlinx.coroutines.experimental.channels.ReceiveChannel
import kotlinx.coroutines.experimental.channels.ClosedSendChannelException

suspend fun consumer( ch: ReceiveChannel<Int> ) {
    try {

        while( true ) loop@ {
            withTimeout( 500 ) {
                val num = ch.receiveOrNull()
                if( num == null ) {
                    println( "[consumer] Channel closed" )
                    return@loop
                }
                else {
                    println( "[consumer] $num received" )
                }
            }
        }
        println( "[consumer] end" )
    } catch( ex: TimeoutCancellationException ) {
        println( ex.toString())
    } finally {
        println( "[consumer] cancel ReceiveChannel" )
        ch.cancel()
    }
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val ch = Channel<Int>()
    val job = launch { consumer( ch ) }

    for( ii in 1..10 ) {
        delay( ii * 100 )
        // チャネルがクローズされた場合、isClosedForSend が true になるが、
        // このチェックをした直後の送信する直前にチャネルがクローズされる可能性があるため、
        // ClosedSendChannelException を catch する
        //if( ch.isClosedForSend ) {
        //    println( "[main] SendChannel was closed before $ii send" )
        //    break
        //}
        try {
            ch.send( ii )
            println( "[main] $ii sended" )
        }
        catch( ex: ClosedSendChannelException ) {
            println( "[main] SendChannel was closed before $ii send" )
            break
        }
    }
    ch.close()
    job.join()
    println( "[main] end" )
}
```

受信側でタイムアウトした時、Channel.cancel() を呼び出すと、送信側で Channel.isClosedForSend プロパティをチェックして、タイムアウトしたかどうかを判定できる。

### produce
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.channels.produce
import kotlinx.coroutines.experimental.channels.consumeEach

fun produceDouble() = produce<Int> {
    for( x in 1..5 ) send( x + x )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val doubles = produceDouble()
    doubles.consumeEach{ println( it ) }
    println( "Done" )
}
```
produce のブロック内で send した値を、produce の戻り値に対する consumeEach で順次受け取る。  
最後の値を受け取ると consumeEach を抜ける。

### actor
```kotlin
import kotlin.system.measureTimeMillis

import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.CompletableDeferred
import kotlinx.coroutines.experimental.CommonPool
import kotlinx.coroutines.experimental.channels.actor
import kotlin.coroutines.experimental.CoroutineContext

suspend fun massiveRun( context: CoroutineContext, action: suspend () -> Unit ) {
    val n = 1000
    val k = 1000
    val time = measureTimeMillis {
        val jobs = List( n ) {
            launch( context ) {
                repeat( k ) { action() }
            }
        }
        jobs.forEach { it.join() }
    }

    println( "Completed ${n * k} actions in $time ms" )
}

sealed class CounterMsg

object IncCounter: CounterMsg()
class GetCounter( val response: CompletableDeferred<Int> ): CounterMsg()

fun counterActor() = actor<CounterMsg> {
    var counter = 0
    for( msg in channel ) {
        when( msg ) {
            is IncCounter -> counter++
            is GetCounter -> msg.response.complete( counter )
        }
    }
    println( "counterActor end" )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val counter = counterActor()
    massiveRun( CommonPool ) {
        counter.send( IncCounter )
    }

    val response = CompletableDeferred<Int>()
    counter.send( GetCounter( response ))
    println( "Counter = ${response.await()}" )
    counter.close()
}
```

### actor その2
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.channels.actor

suspend fun doubleActor() = actor<Int> {
    for( num in channel ) {
        println( num + num )
    }
    println( "doubleActor end" )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val actor = doubleActor()
    for( ii in 1..5 ) {
        actor.send( ii )
    }
    actor.close()
    println( "end" )
}
```

actor は close することで終了させることができるが、次のように parent job を渡し、cancel させることで止めることもできる。

```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.Job
import kotlinx.coroutines.experimental.cancelAndJoin
import kotlinx.coroutines.experimental.channels.actor

suspend fun doubleActor( parentJob: Job ) = actor<Int>( parent = parentJob ) {
    for( num in channel ) {
        println( num + num )
    }
    println( "doubleActor end" )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val job = Job()
    val actor = doubleActor( job )

    val sendJob = launch {
        for( ii in 1..1000 ) {
            delay( 200 )
            actor.send( ii )
        }
    }

    delay( 1000 )

    println( "Job is active?: ${job.isActive}")
    job.cancel()
    job.join()
    println( "Job is active?: ${job.isActive}")

    sendJob.cancelAndJoin()
    println( "end" )
}
```
上記サンプルでは job.cancel() して止めると、「doubleActor end」は表示されない。

### select
#### receive
#### send
#### close
#### delay

### その他
#### 処理中に発生した例外により、チャネルに残ったデータを別のコルーチンが処理する
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.channels.Channel
import kotlinx.coroutines.experimental.channels.toChannel

class ReceiveException( val msg: String, val num: Int ): Exception( msg )

suspend fun receiver( ch: Channel<Int>, altCh: Channel<Int>, name: String, delay: Int ) {
    try {
        for( num in ch ) {
            if( name == "receiver1" && num == 5 ) throw ReceiveException( "[$name] num is $num", num )
            println( "[$name] $num" )
            delay( delay )
        }
        println( "[$name] ** end **" )
    } catch( ex: ReceiveException ) {
        println( ex.toString())
        altCh.send( ex.num )     // 処理できなかったデータを別のチャネルにまわす場合
        ch.toChannel( altCh )    // <== ココ
    }
}

suspend fun sender( ch: Channel<Int>, base: Int, name: String, delay: Int ) {
    for( ii in 1..10 ) {
        ch.send( base + ii )
        delay( delay )
    }
    println( "[$name] ** end **" )
    ch.close()
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    val ch1 = Channel<Int>( 10 )
    val ch2 = Channel<Int>( 10 )

    launch { sender  ( ch1, 0, "sender1", 50 ) }
    val receiver1 = launch { receiver( ch1, ch2, "receiver1", 75 ) }

    val receiver2 = launch { receiver( ch2, ch1, "receiver2", 100 ) }
    launch { sender  ( ch2, 10, "sender2", 50 ) }

    receiver1.join()
    receiver2.join()
}
```
ReceiveChannel<E>#toChannel( channel: SendChannel<E> )  
チャネルに残ったエレメントを指定されたチャネルに転送する

#### Actor が落ちたら、新たに Actor を起動して継続
```kotlin
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.Job
import kotlinx.coroutines.experimental.CoroutineName
import kotlinx.coroutines.experimental.cancelAndJoin
import kotlinx.coroutines.experimental.channels.actor
import kotlinx.coroutines.experimental.channels.ClosedSendChannelException
import kotlin.coroutines.experimental.CoroutineContext

suspend fun doubleActor( parentJob: Job, name: String ) = actor<Int>( parent = parentJob, context = CoroutineName( name ) ) {
    try {
        for( num in channel ) {
            println( "[$name] ${num + num}" )
            if( num == 5 ) throw Exception( "** exception at No. $num" )
        }
        println( "**[$name] doubleActor end" )
    } catch( ex: Exception ) {
        println( "[$name] " + ex.toString())
        channel.cancel()
        cancel()
    }
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    try {
        val job = Job()
        var actor = doubleActor( job, "job1" )

        val sendJob = launch {
            for( ii in 1..10 ) {
                delay( 200 )
                try {
                    actor.send( ii )
                } catch( ex: ClosedSendChannelException ) {
                    println( "[main] " + ex.toString())
                    actor = doubleActor( job, "job2" )    // <== ココ
                    actor.send( ii )                      // 処理できなかったデータを新しい Actor にまわす場合
                }
            }
            actor.close()
            job.cancel()
        }

        println( "Job is active?: ${job.isActive}")
        job.join()
        println( "Job is active?: ${job.isActive}")

        sendJob.join()
        println( "end" )
    } catch( ex: Exception ) {
        System.err.println( ex.toString())
    }
}
```

#### Channel(actor) に CompleteDeffered を渡し、結果を受け取るまで待機する
```kotlin
import kotlin.collections.List
import kotlin.collections.MutableList
import kotlin.system.measureTimeMillis

import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.CompletableDeferred
import kotlinx.coroutines.experimental.channels.actor
import kotlinx.coroutines.experimental.selects.select

data class Hoge( val num: Int, val result: CompletableDeferred<Int> )

fun doubler( capa: Int = 0 ) = actor<Hoge>( capacity = capa ) {
    for( hoge in channel ) {
        delay( 300 )
        hoge.result.complete( hoge.num + hoge.num )
    }

    println( "[doubler] end" )
}

fun main( args: Array<String> ) = runBlocking<Unit> {
    // 結果が返ってきたら次の処理を投げる
    println( measureTimeMillis {
        val doubler = doubler()
        for( ii in 1..5 ) {
            val def = CompletableDeferred<Int>()
            val hoge = Hoge( ii, def )
            doubler.send( hoge )
            println( def.await())
        }
        doubler.close()
    })

    // CompletableDeferred を List に格納し、順に結果を表示
    println( measureTimeMillis {
        val doubler2 = doubler( 5 )
        val list = List( 5 ) { idx ->
            val def = CompletableDeferred<Int>()
            val hoge = Hoge( idx, def )
            doubler2.send( hoge )
            println( "$idx send" )
            def
        }
        doubler2.close()

        list.forEach { ret ->
            println( ret.await())
        }
    })

    // CompletableDeferred を MutableList に格納し、select で処理できた分から結果を表示
    println( measureTimeMillis {
        val doubler3 = doubler( 5 )
        val list = MutableList( 5 ) { idx ->
            val def = CompletableDeferred<Int>()
            val hoge = Hoge( idx, def )
            doubler3.send( hoge )
            println( "$idx send" )
            def
        }
        doubler3.close()

        repeat( 5 ) {
            select<Unit> {
                list.forEach {
                    def -> def.onAwait {
                        println( it )
                        list.remove( def )
                    }
                }
            }
        }
    })

}
```

#### CompleteDeffered を使いまわして再利用する
#### Channel に xxx を渡し、処理が終わると xxx が走る
#### Coroutines を使ってテキストファイルを非同期読み込みする
```
import java.nio.*
import java.nio.channels.*
import java.nio.charset.*
import java.nio.file.*
import kotlinx.coroutines.experimental.runBlocking
import kotlinx.coroutines.experimental.launch
import kotlinx.coroutines.experimental.delay
import kotlinx.coroutines.experimental.yield
import kotlinx.coroutines.experimental.nio.*

fun main( args: Array<String> ) = runBlocking<Unit> {
    val bufSize = 1024
    if( args.size == 0 ) {
        println( "usage: kotlin FileRead3Kt <input file>" )
        System.exit( 1 )
    }
    val filePath = args[0]
    val buf = ByteBuffer.allocate( bufSize )
    val cbuf = CharBuffer.allocate( bufSize )
    val decoder = StandardCharsets.UTF_8.newDecoder()
    val sb = StringBuffer()
    var readed = 0L
    val job = launch{
        AsynchronousFileChannel.open( Paths.get( filePath )).use { ch ->
            while( true ) {
                yield()
                val len = ch.aRead( buf, readed )
                if( len >= 0 ) {
                    buf.flip()
                    val ret = decoder.decode( buf, cbuf, false )
                    cbuf.flip()
                    // println( cbuf )
                    sb.append( cbuf )
                    buf.compact()
                    readed += len
                } else {
                    break
                }
            }
        }

    }

    println( sb )

    job.join()
}
```

(参考) [実例によるkotlinx.coroutinesの手引き](https://github.com/pljp/kotlinx.coroutines/blob/japanese_translation/coroutines-guide.md)
  

# Java11

* HttpClient Sample by Kotlin
  ```kotlin
  import java.net.http.HttpClient
  import java.net.http.HttpRequest
  import java.net.http.HttpResponse
  import java.net.URI
  import java.time.Duration
  
  fun main() {
  
      val hc = HttpClient.newBuilder()
          .version(HttpClient.Version.HTTP_1_1)
          .followRedirects(HttpClient.Redirect.NORMAL)
          .connectTimeout(Duration.ofSeconds(20))
          .build()
  
      val req = HttpRequest.newBuilder()
          .uri(URI.create("https://www.keiji.net/"))
          .build()
  
      // 同期通信
      val res = hc.send( req, HttpResponse.BodyHandlers.ofString())
      println( res.statusCode())
      println( res.body())
  
      println( Thread.currentThread().name )      // main
  
      // 非同期通信
      hc.sendAsync( req, HttpResponse.BodyHandlers.ofString())
          .thenApply{
              println( Thread.currentThread().name )  // ForkJoinPool.commonPool-worker-3
              it.body() 
          }.thenAccept{ 
              println( Thread.currentThread().name )  // ForkJoinPool.commonPool-worker-3
              println(it ) 
          }.join()
  }
  ```

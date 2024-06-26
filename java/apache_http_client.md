
## タイムアウト

Spring の HttpComponentsClientHttpRequestFactory で設定するタイムアウト

* getConnectionRequestTimeout  
  コネクションプールからコネクションが取得できるまでの待ち時間(デフォルト: 3分)  
  [Apache HttpClient 5.x 再利用(使いまわし) 接続設定 ポイント まとめ](https://qiita.com/gosshys/items/df1ea26ba2e8c0860ae4#requestconfig)
* setConnectTimeout  
  接続までのタイムアウト(デフォルト: システムデフォルト)
* setReadTimeout  
  接続してからレスポンスが返ってくるまでのタイムアウト(デフォルト: システムデフォルト)

## リトライ

* HttpClientBuilder で setRetryHandler() を使ったリトライハンドラーの指定しなかった場合、デフォルトで DefaultHttpRequestRetryHandler が使われる。
* DefaultHttpRequestRetryHandler を引数なしで new すると、次のデフォルトパラメーターが適用される。
  * retryCount: 3
  * requestSentRetryEnabled: false  
    requestSentRetryEnabled を true にすると、リクエストを送信した後でエラーが発生した場合もリトライを許可することになる。  
    これは例えば送信したリクエストがサーバー側で受信され、レスポンスが受け取れなかった場合でも再送されることになる。  
    冪等性のないリクエスト(POSTのような)を 2回送信してしまう可能性があるので注意。
* 次の例外はリトライ対象外となる。
  * InterruptedIOException
  * UnknownHostException
  * ConnectException
  * SSLException
* org.apache.commons.httpclient.ConnectTimeoutException(コネクションタイムアウト)は InterruptedIOException のサブクラスなのでリトライされない。
* DefaultHttpRequestRetryHandler に 3つ引数を指定するコンストラクターを使って、無視する(リトライしない) Exception を指定することも可能。


独自例外ハンドラー例

```
private class MyHttpRequestRetryHandler extends DefaultHttpRequestRetryHandler {
    AshHttpRequestRetryHandler(final int retryCount, final boolean requestSentRetryEnabled) {
        super(retryCount, requestSentRetryEnabled);
    }
    @Override
    public boolean retryRequest(IOException exception, int executionCount, HttpContext context) {
        // SomeException はリトライしない
        if(exception instanceof SomeException) {
            System.out.println("Skip (no retry) SomeException.");
            return false;
        // HogeException は 10回リトライ
        } else if(exception instanceof HogeException && executionCount < 10 ) {
            System.out.println("HogeException: " + executionCount + ", " exception.toString());
            return true;
        }

        return super.retryRequest(exception, executionCount, context);
    }
}
```


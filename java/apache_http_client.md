
## タイムアウト

Spring の HttpComponentsClientHttpRequestFactory で設定するタイムアウト

* getConnectionRequestTimeout  
  コネクションプールからコネクションが取得できるまでの待ち時間(デフォルト: 無期限)
* setConnectTimeout  
  接続までのタイムアウト(デフォルト: 無期限)
* setReadTimeout  
  接続してからレスポンスが返ってくるまでのタイムアウト(デフォルト: 無期限)

## リトライ

* 何も指定しないとデフォルトで DefaultHttpRequestRetryHandler が使われる。
* DefaultHttpRequestRetryHandler を引数なしで new すると、次のデフォルトパラメーターが適用される。
  * retryCount: 3
  * requestSentRetryEnabled: false
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


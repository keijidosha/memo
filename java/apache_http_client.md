
## リトライ

* 何も指定しないとデフォルトで DefaultHttpRequestRetryHandler が使われる。
* DefaultHttpRequestRetryHandler を引数なしで new すると、次のデフォルトパラメーターが適用される。
  * retryCount: 3
  * requestSentRetryEnabled: false
* DefaultHttpRequestRetryHandler に 3つ引数を指定するコンストラクターを使って、無視する(リトライしない) Exception を指定することも可能。
* 


```
if (systemProperties) {
    String s = System.getProperty("http.keepAlive", "true");
    if ("true".equalsIgnoreCase(s)) {
        s = System.getProperty("http.maxConnections", "5");
        final int max = Integer.parseInt(s);
        poolingmgr.setDefaultMaxPerRoute(max);
        poolingmgr.setMaxTotal(2 * max);
    }
}
```

```
String userAgentCopy = this.userAgent;
if (userAgentCopy == null) {
    if (systemProperties) {
        userAgentCopy = System.getProperty("http.agent");
    }
    if (userAgentCopy == null) {
        userAgentCopy = VersionInfo.getUserAgent("Apache-HttpClient",
                "org.apache.http.client", getClass());
    }
}
```


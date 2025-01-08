- Table of Content  
{:toc}

# Spring Boot

## プロパティ

* spring.mvc.log-resolved-exception  
  リクエスト受信時のパラメーターチェックでエラーが出た場合のハンドラーを登録すると、リクエスト元にスタックトレースがレスポンスされなくなる代わりに、エラー内容がログ出力されなくなる。  
  このプロパティを true にすると、ハンドルして解決したエラーもログ出力されるようになる。
* spring.jackson.mapper.accept_case_insensitive_properties  
  REST API で受信した JSON の項目名と、Java の DTO クラスに定義したフィールド名の大文字・小文字が違っているとエラーになる。  
  同一視させたい場合は、このプロパティを true にする。  
* application-dev.yml を読み込んで起動  
  次のコマンドラインパラメーターを指定。  
  `-Dspring.profiles.active=dev`
* Log4j 設定ファイルの指定  
  次のコマンドラインパラメーターを指定  
  `-Dlogging.config=/pth/conf/ash-call110br-log4j2-spring.xml`
* Tomcat のリスンポート  
  `server.port`  
  コマンドライン指定  
  ```
  --server.port=9080
  ```
* Tomcat の AJP 有効/無効  
  `custom.ajp.enable`  
  コマンドライン指定で無効にする  
  ```
  --custom.ajp.enable=false
  ```
* Tomcat のアクセスログを出力
  ```yaml
  server:
    tomcat:
      accesslog:
        enabled: true
        directory: /tmp/log/
        suffix: .log
        prefix: tomcat-access_log
        file-date-format: .yyyy-MM-dd
  ```


## Tips

#### Gradleプロジェクトを作成  
1. Spring Initializer を開く  
[https://start.spring.io/](https://start.spring.io/)
1. Gradle Project を選択
    * Java を選択
    * Spring Boot のバージョンを選択
    * Java のバージョンを選択
1. WEBクライアント/サーバーアプリケーションを作成する場合
    * ADD DEPENDENCIES をクリック
    * Spring Web を選択
1. GENERATE をクリック
1. ZIP が DL される
1. DL した ZIP を解凍
1. 解凍したディレクトリを IDEA で開く

#### 組み込みの WEB サーバーが起動しないようにする。  
RestTemplate を使うために `spring-boot-starter-web` を依存すると、実行時に WEBサーバーが起動してしまう。  
これを抑止したい場合、application.properties に次の設定を追加。  
`spring.main.web-application-type=none`


#### FatJAR

* FatJAR を直接起動できる
  * FatJAR にシェルスクリプトが組み込まれている。
  * JAR ファイルと同じディレクトリに JAR ファイルと同名の拡張子 .conf ファイルが存在すると、そのファイルに書かれている内容がコマンドラインパラメーターにセットされる。
  * JAR ファイルがシンボリックリンクの場合は、本体(リンク先)のJAR ファイルがあるディレクトリから .conf ファイルを検索する。
    * JAR ファイル本体があるディレクトリに .conf ファイルが存在しない場合は、シンボリックリンクがあるディレクトリから .conf ファイルを検索する。

#### ブラウザーからH2コンソールへのアクセスを許可する
(例: http://localhost:8080/h2-console)  
application.properties に設定を追記
```
spring.h2.console.enabled=true
```

## Trouble Shooting

### @Controller で @XxxMapping に指定した URL にアクセスすると 404 Not Found になる。
Main クラス配下でない(外側の)パッケージにコントローラーのクラスを作成していた。

### @Asnc を付与したメソッドが非同期実行されない
@Async が付与されたメソッドは、DI されたインスタンスに対して有効になる模様。  
従って直接 new したクラスや、同一クラスの別メソッド呼び出しに対しては有効にならない。


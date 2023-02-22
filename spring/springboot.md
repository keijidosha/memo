# Spring Boot

Table of Contents
=================

* [Tips](#tips)
     * [Gradleプロジェクトを作成](#gradleプロジェクトを作成)
     * [組み込みの WEB サーバーが起動しないようにする。](#組み込みの-web-サーバーが起動しないようにする)
* [Trouble Shooting](#trouble-shooting)
     * [@Controller で @XxxMapping に指定した URL にアクセスすると 404 Not Found になる。](#controller-で-xxxmapping-に指定した-url-にアクセスすると-404-not-found-になる)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)


# Tips

#### Gradleプロジェクトを作成  
1. Spring Initializer を開く  
https://start.spring.io/
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

#### ブラウザーからH2コンソールへのアクセスを許可する
(例: http://localhost:8080/h2-console)  
application.properties に設定を追記
```
spring.h2.console.enabled=true
```

# Trouble Shooting

### @Controller で @XxxMapping に指定した URL にアクセスすると 404 Not Found になる。
Main クラス配下でない(外側の)パッケージにコントローラーのクラスを作成していた。

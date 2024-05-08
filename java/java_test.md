- Table of Content  
{:toc}

## カバレッジ

### jacoco を使ってサーバー環境でカバレッジをとる

1. [jacocoのダウンロードサイト](https://www.jacoco.org/jacoco/) から最新版をダウンロードして解凍
2. jacocoagent.jar をサーバーに配置し、java のコマンドラインにパスを指定。  
   (例)  
   ```
   -javaagent:/var/lib//jacoco/jacocoagent.jar
   ```
1. java プロセスを起動すると、jacoco.exec ファイルが作成される。
2. java のプロセスを停止して jacoco.exec を採取。
3. jacoco の CLI を使って jacoco.exec からカバレッジレポートを生成。  
   ```
   java -jar jacococli.jar report jacoco.exec --classfiles ~/git/hoge/build/classes/java/main/ --sourcefiles ~/git/hoge/src/main/java --html ./jacocoreport --csv ./jacocoreport.csv
   ```  
   => jacocoreport ディレクトリ配下にレポートが出力される。

(参考) [アプリケーション・サーバーでJaCoCoカバレッジを取得する](https://qiita.com/hiromitsu7/items/8b8a050190be06ae18f4)

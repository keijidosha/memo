# Java

## Tips

* JARファイルを解凍せずにマニフェストファイルの内容を表示  
`unzip -p <JARファイル名> META-INF/MANIFEST.MF`

## ランタイムオプション
* -XX:-UseCodeCacheFlushing  
コードキャッシュフラッシングを使わない。これがONになっているとコードキャッシュを使い切ってもログ出力されない。  
コードキャッシュを使い切るとインタープリター実行になるので遅くなる。  
Java7から  
* -XX:ReservedCodeCacheSize=128m  
Java7 では 64MB, Java8 では 256MB がデフォルト?

## OpenJDK8u_162 ソースダウンロード  

1. brew install mercurial  
1. http://openjdk.java.net/projects/jdk8u/  
1. hg clone -r jdk8u162-b12 http://hg.openjdk.java.net/jdk8u/jdk8u jdk8u162-b12  
1. sh get_source.sh  

- Table of Content  
{:toc}

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

## トラブルシューティング

* NIC が 2つある環境で動作する JVM に JMX 接続できない(接続がタイムアウトする)。    
  JVM 実行 OS 側で `hostname -i` を実行して表示される IP アドレスが、JMX クライアントから見て接続先サーバーの IP アドレスになっているかを確認。  
  なっていない場合は /etc/hosts を編集して、接続先 IP アドレスになるようにする。  
  (例) ホスト hoge で `hostname -i` IP を実行して 192.168.1.1 になるように /etc/hosts を編集する。  
  ```
  192.168.1.1 hoge
  ```

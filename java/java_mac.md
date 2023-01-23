# Mac で Java

* インストールされている Java のバージョンとディレクトリを確認する  
  /usr/libexec/java_home -V
  ```
  Matching Java Virtual Machines (4):
      1.8.0_111, x86_64: "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home
      1.7.0_80, x86_64: "Java SE 7" /Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
      1.6.0_65-b14-468, x86_64: "Java SE 6" /Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
      1.6.0_65-b14-468, i386: "Java SE 6" /Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
  ```
* 複数のバージョンの Java がインストールされた Mac で、Java のバージョンに合わせた JAVA_HOME 環境変数をセットする。  
  export JAVA_HOME=`/usr/libexec/java_home -v <バージョン指定>`  
  (例)  
  ```
  export JAVA_HOME=`/usr/libexec/java_home -v 1.8`
  echo $JAVA_HOME => /Library/Java/JavaVirtualMachines/jdk1.8.0_60.jdk/Contents/Home
  ```


## Sierra と Oracle Java 1.8 の組み合わせで Java が極端に遅い。(動き出すまでに 5秒かかったり、30秒かかったり)

今回は Mac OS Sierra  + Oracle JDK 1.8u111 の組み合わせで Log4j2 使ってログ出力すると、実行に 5秒かかる現象が発生。  
Oracle Java 1.8u65 以降、Sierra で DNS参照が上手くいかないのが原因らしい。  
(回避策)  
/etc/hosts に次の行を追加。  
\<hostname> はターミナルから hostname コマンドを実行して表示される名前を入力。  
```
127.0.0.1   localhost <hostname>
::1         localhost <hostname>
```
(参考)  
http://nabedge.mixer2.org/2016/12/macos-jdk-slow-issue.html  
http://stackoverflow.com/questions/33289695/inetaddress-getlocalhost-slow-to-run-30-seconds/40487173  
http://stackoverflow.com/questions/39636792/jvm-takes-a-long-time-to-resolve-ip-address-for-localhost/39698914  
https://thoeni.io/post/macos-sierra-java/  
http://bugs.java.com/bugdatabase/view_bug.do?bug_id=8143378  

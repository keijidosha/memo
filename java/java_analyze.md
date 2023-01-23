# JVM, メモリー状況取得, 障害対応

### コマンドいろいろ

* Java VM の設定一覧詳細を表示
java -XX:+PrintFlagsFinal -version -server
* jps
Javaプロセス一覧
* jstack
スレッドダンプ取得
jstack <プロセスID>
* jmap
  * ヒープ使用状況、ヒープ設定パラメータ(NewRatio, SurvivorRatioなど)
    jmap -heap <プロセスID>
  * ネイティブメモリー使用状況
    jmap <プロセスID>
  * ヒープダンプ
    jmap -dump:format=b,file=heap.dump <プロセスID>
* jinfo
  * JVM設定表示
    jinfo <プロセスID>
  * フラグ設定値だけを表示
    jinfo -flags <プロセスID>
  * フラグ設定値を変更
    jinfo -flag key=value <プロセスID>
* lsof(Linuxコマンド)  
参照ファイル一覧  
lsof <プロセスID>  
lsof -u ユーザーID  
* jstat  
  * 統計情報出力  
param="-class -compiler -gc -gccapacity -gccause -gcmetacapacity -gcnew -gcnewcapacity -gcold -gcoldcapacity -gcutil -printcompilation"; for p in $param; do echo $p; jstat $p \`jps | grep <メインクラス名> | awk '{print $1;}'\`; done  
  * 上のコマンドをタブ区切りにする  
param="-class -compiler -gc -gccapacity -gccause -gcmetacapacity -gcnew -gcnewcapacity -gcold -gcoldcapacity -gcutil -printcompilation"; for p in $param; do jstat $p \`jps | grep MainKt | awk '{print $1;}'\` | sed 's/^  *//' | sed 's/  */\t/g'; done > hoge.txt
  * 日付付きで GC ログ出力(ヘッダーは出力しない)
echo -n `date +%Y/%m/%d-%H:%M:%S`"      " >> gc.log
jstat -gc \`jps | grep MainKt | awk '{print $1;}'\` | sed -e '/S0C.*/d'  | sed 's/^  *//' | sed 's/  */\t/g' >> gc.log
* jcmd  
重要: 対象 Javaプロセスを実行しているのと同じユーザで実行する  
  * 診断情報を出力  
jcmd <プロセスID|メインクラス名> PerfCounter.print  
「sun.rt.createVmBeginTime」の数値が起動時刻になるのかも。  
(例) sun.rt.createVmBeginTime=1531354125170 の場合  
System.out.println( new Date( 1531354125170 ));  
  * すべてのパラメーターを一覧を表示  
jcmd <プロセスID> VM.flags -all
  * Full GC を実行  
jcmd <プロセスID> GC.run
  * プロセスに対して使用可能なコマンド一覧を表示  
jcmd <プロセスID|メインクラス名> help

### ヒープ
#### ヒープダンプを取得して MemoryAnalyzer で分析

* jmap -dump:format=b,file=<出力パス> -F <プロセスID>  
(例) jmap -dump:format=b,file=/tmp/heap.dump -F 1001  
-F は強制出力。-F を付けないとヒープダンプが取得できないことが多いが、-F なしで取得できるなら付けなくて良い。
* http://www.eclipse.org/mat/ から Memory Analyzer (MAT) をダウンロードして読み込ませる。

#### ヒープダンプに残っている文字列をカウントして多い順にソート

* strings -a java_pidxxx.hprof | sort | uniq -c | sort -nr | less

(参考) http://blog.cybozu.io/entry/2015/12/01/110000

#### ヒープ構成とコマンドライン指定

<table>
<tr><td>(1)</td><td>Eden</td><td>S0</td><td>S1</td><td>Old</td></tr>
<tr><td>(2)</td><td colspan=4 align="center">64MB</td></tr>
<tr><td>(3)</td><td colspan=3 align="center">1</td><td>2</td></tr>
<tr><td>(4)</td><td colspan=3 align="center">20MB</td></tr>
<tr><td>(5)</td><td align="center">0</td><td align="center">1</td><td align="center">1</td></tr>
</table>

(1) 新しいオブジェクトは Eden に割り当てられる。その後 S0 と S1 を行ったり来たりして参照が残っている(解放されなかった)場合は Old に移動される。

(2) -Xmx: 64m: Eden + S0 + S1 + Old 合計で 64MB 割り当てる

(3) -XX:NewRatio=2: Eden + S0 + S1 合計の 2倍(1対2の比率)になるよう Old領域を割り当てる

(4) -XX:NewSize=20m: Eden + S0 + S1 が 20MB になるよう割り当てる  
※NewRatio か NewSize のどちらか一方を指定する(両方は指定しない)。

(5) -XX:SurvivorRatio=8: Eden : S0 : S1 の比率が 8:1:1 になるよう割り当てる

(参考) http://equj65.net/tech/java8hotspot/


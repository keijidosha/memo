# diff

* リモートとローカルのファイルを比較  
`ssh 192.168.1.1 cat /tmp/hoge.txt | diff /tmp/hoge.txt  -`  
参考: [サーバ間でdiffをとる方法](http://dqn.sakusakutto.jp/2014/03/diff_server.html)
* Unified形式で表示する時の、前後の行数を指定する  
(例) 差異がある行の前後を表示しない(0行)  
`diff -U0 hoge.txt fuga.txt`

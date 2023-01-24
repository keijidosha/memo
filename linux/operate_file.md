# ファイル操作

* 最初の 5行を表示  
`head -n 5 hoge.txt`
* 最後の 5行を表示  
`tail -n 5 hoge.txt`
* 10から15行目を表示  
`sed -n -e '10,15p' hoge.txt`
* 20行目だけを表示  
`sed -n -e '20p' hoge.txt`
* 16進数表示  
`hexdump -C hoge.bin`

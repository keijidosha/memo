# pv(Pipe Viewer)

* 速度制限しながらコピーする  
1Mbps でコピー  
`cat hoge.bin | pv -L 1m > /tmp/hoge.bin`  
100Kbps でコピー  
`cat hoge.bin | pv -L 100k > /tmp/hoge.bin`

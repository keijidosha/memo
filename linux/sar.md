- Table of Content  
{:toc}

# sar

## オプション

* -P ALL: CPU 使用率
* -r: メモリ
* -q: キュー(load average 等)
* -S: スワップ領域
* -W: 1秒間にスワップイン/アウトされたページ数
* -b: ディスク I/O
* -d -p: ディスク I/O
* ネットワーク
  * -n DEV: パケット数


### グラフ化

* ネットワーク状況を SVG 出力
  ```
  sadf -g sa01 -- -n ALL > network_01.svg
  ```


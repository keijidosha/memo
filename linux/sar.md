- Table of Content  
{:toc}

# sar

## オプション

* -P ALL: CPU 使用率
* -u 1: 全 CPU 平均の使用率
* -r: メモリ
* -q: キュー(load average)
* -w: コンテキストスイッチ
* -S: スワップ領域
* -W: 1秒間にスワップイン/アウトされたページ数
* -b: ディスク I/O
* -d -p: ディスク I/O
* ネットワーク
  * -n DEV: パケット数
  * -n EDEV: エラー数?
  * -n SOCK: ソケットの使用状況
  * -n SOFT: software-based network processing


### グラフ化

* ネットワーク状況を SVG 出力
  ```
  sadf -g sa01 -- -n ALL > network_01.svg
  ```


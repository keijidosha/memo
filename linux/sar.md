- Table of Content  
{:toc}

# sar

```
sar -f sa01
```

## オプション

* -P ALL: CPU 使用率
* -u 1: 全 CPU 平均の使用率
* -r: メモリ
* -q: キュー(load average)
* -w: コンテキストスイッチ
* -S: スワップ領域
* -W: 1秒間にスワップイン/アウトされたページ数
* -b: I/O 統計
* -d -p: ディスク I/O
* ネットワーク
  * -n DEV: パケット数
  * -n EDEV: エラー数?
  * -n SOCK: ソケットの使用状況
  * -n SOFT: software-based network processing


### グラフ化

* ネットワーク状況を SVG 出力
  ```
  sadf -T -g sa01 -- -n ALL > network_01.svg
  ```

## Ubuntu 24.04 に sar コマンドを仕掛ける

* sudo apt install sysstat  
  /etc/default/sysstat が「ENABLED="true"」になっているか確認  
  自動起動設定
  ```
  sudo systemctl enable sysstat
  sudo systemctl start sysstat
  ```
* 取得間隔を 1分にする  
  ```
  sudo mkdir /etc/systemd/system/sysstat-collect.timer.d/
  sudo vim /etc/systemd/system/sysstat-collect.timer.d/override.conf
  ```
  ```
  [Timer]
  OnCalendar=
  OnCalendar=*:00/1
  ```
  ```
  sudo systemctl daemon-reexec
  sudo systemctl restart sysstat
  ```
* 保存期間を 1週間にする  
  /etc/sysstat/sysstat で次のように設定
  ```
  HISTORY=7
  ```



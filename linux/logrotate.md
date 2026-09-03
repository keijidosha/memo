- Table of Content  
{:toc}

# logrotate

## 実行スケジュール

* /etc/cron.daily/logrotate から実行されている。
* cat /etc/anacrontab
  ```
  RANDOM_DELAY=45
  START_HOURS_RANGE=3-22
  #period in days   delay in minutes   job-identifier   command
  1                 5                  cron.daily       nice run-parts /etc/cron.daily
  ```
  /etc/cron.daily 配下のファイルは 3:05 ～ 3:50 の間のランダムな時間に実行される。

## 手動実行

* 空実行(dryrun)
  ```
  sudo logrotate -dv /etc/logrotate.d/xxx.conf
  ```
* 強制実行
  ```
  sudo logrotate -fv /etc/logrotate.d/xxx.conf
  ```

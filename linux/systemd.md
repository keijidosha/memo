- Table of Content  
{:toc}

# systemd

## timer

* timer サービスを作成
  * /etc/systemd/system/xxx.timer を作成
    ```
    [Unit]
    Description=Run xxx.service every day

    [Timer]
    OnCalendar=daily
    Persistent=true

    [Install]
    WantedBy=timers.target
    ```
  * /etc/systemd/system/xxx.service を作成
    ```
    [Unit]
    Description=Daily running script xxx.sh

    [Service]
    Type=oneshot
    # 実行するスクリプト
    ExecStart=/usr/local/bin/xxx.sh
    User=hoge
    ```
  * リロードして反映
    ```
    sudo systemctl daemon-reload
    ```
* timer サービスの前回実行時刻と次回実行予定時刻を表示
  ```
  systemctl list-timers xxx.timer
  ```
* 意図的に前回実行時刻を変更して前日は実行していなかったことにする。
  ```
  sudo systemctl stop xxx.timer
  sudo touch -d "2 days ago" /var/lib/systemd/timers/stamp-xxx.timer
  sudo systemctl start xxx.timer
  ```

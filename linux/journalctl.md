- Table of Content  
{:toc}

# journalctl

## パラメーター

* -x: 説明を表示
* -e: ページャーを使用
* -u <サービス名>: 指定したサービスだけの情報を表示

## Tips

* 表示するログの期間を指定
  ```
  sudo journalctl --since='2025-09-01 10:00:00' --until='2025-09-01 11:00:00'
  ```

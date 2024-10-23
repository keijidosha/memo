# dnf


## パッケージ

* パッケージの一覧  
  `dnf list ansible`  
  または探したいパッケージ名をワイルドカード指定  
  `dnf list "python3*`
* 依存パッケージを表示  
  `dnf deplist xxx`
* インストール済み RPM の一覧  
  `dnf list --installed`  
  RPM 名を指定  
  `dnf list --installed "java*"`  

## モジュール

* モジュールの一覧  
`dnf module list`
* AppStream からインストールするモジュールで指定可能なバージョンの一覧  
`dnf module list nginx`
* AppStream からインストールするモジュールのバージョンを設定  
`dnf module enable nginx:1.18`
* モジュールのバージョン設定をリセット  
`dnf module reset nginx`
* モジュール削除  
  `dnf erase xxx`

## Tips

* キャッシュクリア
  ```
  dnf clean all
  rm -rf /var/cache/dnf/
  ```

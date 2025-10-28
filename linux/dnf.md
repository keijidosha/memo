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
* パッケージの削除  
  `dnf erase <packagename>`

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

## リポジトリ

* リポジトリ同期(ダウンロード)
  ```
  dnf [-c xxx.conf] reposync --repo=<repo_name> -p <download directory> --download-metadata
  ```
  * 途中で止めた場合、次回実行時にダウンロード済みのものはスキップしてくれる。
  * ダウンロード済みかどうかの判定にハッシュ値チェックをしているのか、RPM ファイルのサイズが大きいと、チェックにそれなりの時間がかかる。

## Tips

* キャッシュクリア
  ```
  dnf clean all
  rm -rf /var/cache/dnf/
  ```
* dnf install を実行すると yes/no を聞いてこないままインストールまで実行されてしまう。  
  /etc/dnf/dnf.conf で「assumeyes=True」になっていないか確認する。

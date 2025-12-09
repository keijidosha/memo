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

## インストール

* xxx- で始まる yum リポジトリだけを有効にする
  ```
  dnf --disablerepo=* --enablerepo=xxx-* install hoge
  ```
* 弱い依存関係(weak dependencies)のパッケージをインストールしない
  ```
  dnf --setopt=install_weak_deps=False install hoge
  ```

## 履歴

* dnf のインストール履歴を参照
  ```
  dnf history
  ```
* 履歴の内容を表示  
  ansible で実行した dnf install は、履歴の一覧に内容が表示されないので、トランザクションID を指定して内容の確認が必要
  ```
  dnf history info <transactionid>
  ```
* dnf install をロールバック  
  トランザクションID を指定して、インストールをなかったことにする。
  ```
  sudo dnf history undo <transactionid>
  ```
  途中の(最後のではなく)履歴をアンインストールできるかどうかは不明(要確認)。

## リポジトリ

* リポジトリ同期(ダウンロード)
  ```
  dnf [-c xxx.conf] reposync --repo=<repo_name> -p <download directory> --download-metadata [--setopt=max_parallel_downloads=1]
  ```
  * 1個ずつダウンロードする場合は「--setopt=max_parallel_downloads=1」を指定。
  * 途中で止めた場合、次回実行時にダウンロード済みのものはスキップしてくれる。
  * ダウンロード済みかどうかの判定にハッシュ値チェックをしているのか、RPM ファイルのサイズが大きいと、チェックにそれなりの時間がかかる。
  * 再実行して「No more mirrors to try - All mirrors were already tried without success」というメッセージが表示された場合は `sudo dnf clean all` を実行してみる。

## Tips

* キャッシュクリア
  ```
  dnf clean all
  rm -rf /var/cache/dnf/
  ```
* dnf install を実行すると yes/no を聞いてこないままインストールまで実行されてしまう。  
  /etc/dnf/dnf.conf で「assumeyes=True」になっていないか確認する。

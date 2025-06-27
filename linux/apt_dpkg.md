- Table of Content  
{:toc}

# apt/dpkg

* パッケージを検索
  ```
  apt search <pkgname>
  ```
* パッケージがインストールされているか確認
  ```
  apt list --installed <pkgname>
  ```
* deb ファイルの中身を確認
  ```
  ar x xxx.dev
  ```
  さらに中身を展開
  ```
  tar xf xxx.xz
  ```
* 依存モジュールごとパッケージをダウンロード
  ```
  apt update
  apt install --download-only -o Dir::Cache::Archives="/root" openjdk-8-jdk
  ```
  インストール済みのパッケージも含めてダウンロードする場合は --reinstall を指定
  ```
  apt update
  apt install --reinstall --download-only -o Dir::Cache::Archives="/root" openjdk-8-jdk
  ```

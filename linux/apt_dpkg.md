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
  または
  ```
  dpkg -l <pkgname>
  または
  dpkg -l | grep <pkgname>
  ```
* deb ファイルの中身を確認
  ```
  ar x xxx.deb
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
* インストール済み deb パッケージを新しいバージョンの deb パッケージで更新
  ```
  sudo apt install --dry-run ./<pkgname>.deb
  sudo apt install ./<pkgname>.deb
  ```

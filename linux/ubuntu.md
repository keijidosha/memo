- Table of Content  
{:toc}

# Ubuntu

* 依存モジュールごとパッケージをダウンロード
  ```
  apt update
  apt install --download-only -o Dir::Cache::Archives="/root" openjdk-8-jdk
  ```

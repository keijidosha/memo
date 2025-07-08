- Table of Content  
{:toc}

# Ubuntu

## コアダンプ

* コアダンプをファイルに出力させる設定
  ```
  sudo sysctl -w kernel.core_pattern="/tmp/core.%e.%p.%t"
  ulimit -S -c unlimited
  ```
  これもしておいた方が良いか?
  ```
  echo "/tmp/core.%e.%p" | sudo tee /proc/sys/kernel/core_pattern
  echo "kernel.core_pattern = /tmp/core.%e.%p" | sudo tee -a /etc/sysctl.conf
  ```
  => /tmp に core で始まるファイルが出力される。
  * 恒久的に設定する場合は /etc/security/limits.conf に追記
    ```
    *               soft    core            unlimited
    *               hard    core            unlimited
    ```
  * apport のサービスを止める
    ```
    sudo systemctl stop apport.service
    sudo systemctl disable apport.service
    ```
* 意図的にコアを吐かせるサンプル(コアダンプが出力される確認するための)
  ```
  #include <stdio.h>

  int main() {
      int *p = NULL;
      *p = 42;  // NULLポインタに書き込み → セグフォールト
      return 0;
  }
  ```
  ビルド
  ```
  gcc -o segfault segfault.c
  ./segfault
  ```
  または
  ```
  sleep 100 &
  kill -SEGV $!
  ```
* バックトレース
  ```
  sudo apt update
  sudo apt install gdb
  gdb <command> <dumpfile>
  ```


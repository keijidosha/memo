- Table of Content  
{:toc}

# Ubuntu

## コアダンプ

* コアダンプをファイルに出力させる設定
  ```
  sudo sysctl -w kernel.core_pattern="/tmp/core.%e.%p.%t"
  ulimit -S -c unlimited
  ```
  => /tmp に core で始まるファイルが出力される。
  * 恒久的に設定する場合は /etc/security/limits.conf に追記
    ```
    *               soft    core            unlimited
    *               hard    core            unlimited
    ```
  * サービス実行の場合は、次の設定を追加?
    ```
    [Service]
    LimitCORE=infinity
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



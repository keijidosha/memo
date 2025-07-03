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



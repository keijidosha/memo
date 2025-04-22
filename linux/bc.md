≥- Table of Content  
{:toc}

# bc

* 10進数を 2進数で表示
  ```
  echo "ibase=A; obase=2; 255" | bc
  ```
* 2進数を 10進数で表示
  ```
  echo "ibase=2; obase=A; 10101010" | bc
  ```

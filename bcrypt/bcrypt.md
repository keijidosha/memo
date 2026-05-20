# bcrypt

## bcryptハッシュ生成


## bcryptハッシュ検証

* python
  ```
  python3 -c 'import crypt; h="<hash>"; p="<password>"; print("MATCH") if crypt.crypt(p, h) == h else print("FAIL")'
  ```
  ※Python 3.12 まで crypt ライブラリが使用可能。3.13 からは不可。
* Ruby
  ```
  ruby -e 'require "bcrypt"; puts BCrypt::Password.new("<hash>") == "<password>" ? "OK" : "NG"'
  ```
  ※bcrypt の gem が必要。
* PHP
  ```
  php -r 'echo password_verify("<password>", "<hash>") ? "OK\n" : "NG\n";'
  ```

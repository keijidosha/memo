# tbls

## 準備

.tbls.yml を作成  
(例)

```
dsn: postgres://postgres:postgresql@192.168.1.1:5432/dbname?sslmode=disable

# ドキュメントのディレクトリを指定します
docPath: doc/schema

er:
  skip: true

# 作成するテーブルのリスト
include:
  - user*
  - book*
exclude:
  - user_private*
```

## 実行

```
tbls doc
```

## 参考

[https://github.com/k1LoW/tbls](https://github.com/k1LoW/tbls)  
[https://qiita.com/k1LoW/items/2010413a8547b1e6645e](https://qiita.com/k1LoW/items/2010413a8547b1e6645e)

- Table of Content  
{:toc}

# PostgreSQL

## psql

### コマンド
* 拡張表示  
  `\x`
* 接続先情報を環境変数に設定
  ```
  export PGHOST=192.168.1.1
  export PGPORT=65432
  export PGUSER=hoge
  ```
* コマンド履歴から重複排除  
  .psqlrc に次の設定を記述
  ```
  \set HISTCONTROL ignoredups
  \set COMP_KEYWORD_CASE upper
  ```
* 検索結果をタブ区切りで出力  
  ```
  psql -F "<tab>" -Atc "SELECT xxx FROM xxx"
  ```  
  -F: 区切り文字, &lt;tab> は contrl + V, I で入力(I は A から数えて 9番目)  
  -A: 桁揃えなし(no align)  
  -t: 結果のみを表示(tuple only)


## テーブル作成(サンプル)

### シンプルなサンプル

```
CREATE TABLE hoge(
  id bigint NOT NULL PRIMARY KEY GENERATED ALWAYS  AS IDENTITY,
  name varchar(16)
);
```
id という名前でプライマリーキーを作成し、シーケンスで採番する。

### パーティションテーブル

```
CREATE TABLE hoge(
  date timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL PRIMARY KEY,
  name varchar(16)
) PARTITION BY RANGE(date);

CREATE TABLE hoge_202301 PARTITION OF hoge FOR VALUES FROM ('2023-01-01 00:00:00') TO ('2023-02-01 00:00:00');
```
* date カラムでパーテションテーブルに振り分ける。  
* 親テーブルにプライマリーキーやインデックスを指定しておくと、パーティションテーブルを作成する時に自動的にプライマリーキーやインデックスが作成される。  
  ```
  \d hoge_202301
                            Table "public.hoge_202301"
   Column |           Type           | Collation | Nullable |      Default      
  --------+--------------------------+-----------+----------+-------------------
   date   | timestamp with time zone |           | not null | CURRENT_TIMESTAMP
   name   | character varying(16)    |           |          | 
  Partition of: hoge FOR VALUES FROM ('2023-01-01 00:00:00+00') TO ('2023-02-01 00:00:00+00')
  Indexes:
      "hoge_202301_pkey" PRIMARY KEY, btree (date)
  ```
* ただし、親テーブルにプライマリーキーを指定する場合は、そのカラムが RANGE にも指定されている必要がある。  
  RANGE に指定したカラムとは別のカラムをプライマリーキーにする必要がある場合、次のようにプライマリーキーを各パーティションテーブルごとに個別に指定する。
  ```
  CREATE TABLE hoge(
    id bigint NOT NULL GENERATED ALWAYS AS IDENTITY,
    date timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    name varchar(16)
  ) PARTITION BY RANGE(date);
  
  CREATE TABLE hoge_202301 PARTITION OF hoge FOR VALUES FROM ('2023-01-01 00:00:00') TO ('2023-02-01 00:00:00');
  ALTER TABLE ONLY hoge_202301 ADD CONSTRAINT hoge_202301_pkey PRIMARY KEY (id);
  ```
  次のようにプライマリーキーとは別のカラムでパーテション化される。
  ```
  \d hoge_202301
                            Table "public.hoge_202301"
   Column |           Type           | Collation | Nullable |      Default      
  --------+--------------------------+-----------+----------+-------------------
   id     | bigint                   |           | not null | 
   date   | timestamp with time zone |           | not null | CURRENT_TIMESTAMP
   name   | character varying(16)    |           |          | 
  Partition of: hoge FOR VALUES FROM ('2023-01-01 00:00:00+00') TO ('2023-02-01 00:00:00+00')
  Indexes:
      "hoge_202301_pkey" PRIMARY KEY, btree (id)
  ```
  * RANGE にプライマリーキーを指定する場合であっても、SUBSTRING(id,1,2) のようにファンクションを使うと、親テーブルにプライマリーキーは指定できない。

## プライマリキー

* プライマリキーが存在しない場合だけ作成する(ワンライナー)  
  ```
  DO $do$ BEGIN IF NOT EXISTS ( SELECT 1 FROM pg_constraint con JOIN pg_class cls ON con.conrelid = cls.oid WHERE con.contype = 'p' AND cls.relname = 'primary_key_name' ) THEN ALTER TABLE IF EXISTS table_name ADD CONSTRAINT primay_key_name PRIMARY KEY (pk_column); END IF; END $do$;
  ```

## SQL

### 正規表現

* ~(チルダ): 正規表現に一致する、大文字・小文字を区別
* ~*: 正規表現に一致する、大文字・小文字を区別しない
* !~: 正規表現に一致しない、大文字・小文字を区別
* !~*: 正規表現に一致しない、大文字・小文字を区別しない

## エクスポート・インポート

### シンプル・テキストベース

* 特定のテーブルのデータだけをエクスポート  
  `pg_dump -U <ユーザーID> --data-only -t <テーブル名> -d <DB名> > xxx.sql`
* インポート  
  `psql -f xxx.sql <DB名>`

### 別ホストに複製

* 事前にデータベースを作成して別ホストからインポート
  ```
  pg_dump -h 192.168.1.1 dbname | psql dbname
  ```
* バイナリ形式で別ホストからインポート
  ```
  pg_dump -Fc -h 192.168.1.1 dbname | pg_restore -Fc -d dbname
  ```
* データベース全体を別ホストから移行
  ```
  pg_dumpall -h 192.168.1.1 | psql
  ```
* gzip ファイル経由でデータベース全体を別ホストから移行
  ```
  pg_dumpall | gzip > dumpfile
  zcat dumpfile | psql postgres
  ```
* オーナーなしでエクスポートすることで、オーナーを変更してインポート
  エクスポート
  ```
  pg_dump -Fc -O dbname > dbname.dump
  ```
  DB を作成してインポート
  ```
  psql
  create user someuser;
  create database somedb with owner=someuser encoding=UTF8;
  \q
  psql -d somedb
  alter schema public owner to someuser;
  \q
  ```

### パラメーター

* -Fc  
  --format=c|t:specify backup file format  
  ファイル形式はカスタム
* -O  
  --no-owner:skip restoration of object ownership  
  オーナーを指定しない
* -x  
  --no-privileges:skip restoration of access privileges (grant/revoke)  
  アクセス権限(grant/revoke)を設定しない
* -U  
  --username=NAME:connect as specified database user  
  インポートに使うユーザを指定
* -d  
  --dbname=NAME:connect to database name  
  インポート対象DBを指定


## シーケンス

* シーケンスの値を変更する  
`select setval('シーケンス名',10);`
* シーケンスの値を初期化する(値を 1 にし、状態を呼び出し前にする)  
`select setval('シーケンス名',1,false);`
* シーケンスの値を確認する  
`select currval('シーケンス名');`
* シーケンスの状態を確認する  
`\d シーケンス名`  
currval of sequence "xxx" is not yet defined in this session というエラーが出る場合は  
`select last_value from <シーケンス名>;`

## 関数

* 日付計算  
  * 1時間後
    `SELECT CURRENT_TIMESTAMP + '1 hour'`
  * 30分後  
    (例1) `SELECT CURRENT_TIMESTAMP + '30 minute'`  
    (例2) `SELECT CURRENT_TIMESTAMP + time '00:30'`
  * 30分前  
    `SELECT CURRENT_TIMESTAMP - time '00:30'`

## ログ

* 発行した SQL をログ出力する  
postgresqlconf に次の指定をする  
`log_statement='all'`

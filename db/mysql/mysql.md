# MySQL

Table of Contents
-----------------

* [MySQL](#mysql)
  * [操作](#操作)
    * [起動/停止](#起動停止)
    * [シェルスクリプト](#シェルスクリプト)
    * [mysqlコマンド](#mysqlコマンド)
      * [データベース](#データベース)
      * [テーブル](#テーブル)
      * [ユーザと権限](#ユーザと権限)
      * [ストレージエンジン](#ストレージエンジン)
      * [エンコーディング関連](#エンコーディング関連)
      * [状態](#状態)
      * [レプリケーション](#レプリケーション)
  * [設定](#設定)
      * [スキーマ作成](#スキーマ作成)
      * [管理](#管理)
        * [バックアップ](#バックアップ)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)
---

## 操作

### 起動/停止

* 起動  
cd /usr/local/mysql; sudo ./bin/safe_mysqld –user=mysql &
* 停止  
sudo mysqladmin shutdown
* 安全な起動/停止  
mysqld_safe –relay-log=/var/lib/mysql/mysql-relay-bin –relay-log-index=/var/lib/mysql/mysql-relay-bin -O max_connect_errors=1000 &  
mysqladmin shutdown -u root -p

### シェルスクリプト

```
export PATH=/usr/bin:/bin:/usr/local/mysql/bin
case $1 in
 start)
    cd /usr/local/mysql; ./bin/safe_mysqld --user=mysql &
    ;;
  stop)
    mysqladmin shutdown
    ;;
  *)
    echo "usage `basename $0` (start|stop)"
    ;;
esac
```

### mysqlコマンド

* ログイン  
mysql -u root -p
* ログアウト  
\q
* コマンドラインから直接 SQL を実行  
mysql -u <user> -p<password> <database> -e ”<SQL>“  
※-p とパスワードの間にスペースを入れない。
* 接続状況を表示  
status;

#### データベース

* データベース一覧表示  
show databases;
* データベース作成  
create database <データベース名>;
* データベース選択  
use <データベース名>;

#### テーブル

* テーブル一覧表示  
show tables;
* テーブル構造表示  
desc <テーブル名>;
* データベース名を指定して、テーブルの状態を表示する  
show table status from <データベース名>;

#### ユーザと権限

* ユーザ一覧表示  
SELECT host,user FROM mysql.user;
* ユーザ作成  
GRANT ALL PRIVILEGES ON <データベース名>.* TO <ユーザ名> IDENTIFIED BY '<パスワード>';
* ユーザー権限表示  
SHOW GRANTS FOR <user>;
* パスワード変更  
SET PASSWORD FOR <user>@'<host>' = password('<new password>');
* ユーザ削除  
DELETE FROM mysql.user WHERE user='<ユーザ名>';  
DELETE FROM mysql.user WHERE user='<ユーザ名>' and host='<ホスト名>';
* 合わせて権限も削除  
REVOKE ALL PRIVILEGES ON * . * FROM <ユーザ名>;  
REVOKE ALL PRIVILEGES ON * . * FROM <ユーザ名>@'<ホスト名>';
* 権限の再読み込み  
FLUSH PRIVILEGES;

#### ストレージエンジン

* テーブルのストレージエンジンを確認  
  * データベースに含まれるすべてのテーブルを確認  
SHOW TABLE STATUS FROM <データベース名>;
  * テーブルごとに確認  
SHOW CREATE TABLE <テーブル名>;
* 作成済みテーブルのストレージエンジンを変更  
ALTER TABLE <テーブル名> ENGINE=<エンジン名>;
* テーブル作成時にエンジンを指定する  
CREATE TABLE <テーブル名> … ENGINE=<エンジン名>;
* 使用可能なエンジンの一覧と状況を表示  
SHOW ENGINES;
* デフォルトストレージエンジンの変更  
  * vi /etc/mysql/my.cnf
    ```
    [mysqld]
    default-storage-engine=INNODB
    ストレージエンジンの状態を確認
    show engine innodb status\G
    ```

#### エンコーディング関連

* データベースのエンコーディングを確認する。  
SHOW CREATE DATABASE <データベース名>;
* 作成済みデータベースのエンコーディングを変更する。  
alter database <データベース名> default character set utf8;
* デフォルトエンコーディングを UTF-8 にする。  
  * /etc/mysql/my.cnf を編集。
    ```
    [client]
    default-character-set=utf8
    [mysqld]
    default-character-set=utf8
    character-set-server=utf8
    [mysqldump]
    default-character-set=utf8
    [mysql]
    default-character-set=utf8
    ```
* CHARSET 関連の設定を表示。  
SHOW variables LIKE '%char%';

#### 状態

* グローバル変数の一覧を表示  
show global variables;
* セッション変数の一覧を表示  
show session variables;

#### レプリケーション

* スレーブの状態表示  
show slave status;
* レプリケーションフォーマットの表示  
show variables where variable_name='binlog_format';

* レプリケーションフォーマットについて  
  > MySQL 5.1.8 より、第 3 のオプションが利用可能になりました。ミックス ベース レプリケーション (MBR) です。MBR では、デフォルトでステートメント ベース レプリケーションが行われますが、自動的に行ベース レプリケーションに切り替わります。次のケースがそれに該当します。項5.1.2.2. 「ミックス レプリケーション フォーマット」 も参照してください。
* MIXED モードで実行している場合、次の条件下にあるレプリケーションはステートメント ベースから行ベースに自動的に切り替わります。
  * DML ステートメントが NDB テーブルを更新するとき
  * 関数に UUID() が含まれているとき
  * AUTO_INCREMENT カラムを伴う 2 つ以上のテーブルを更新するとき
  * INSERT DELAYED を実行するとき
  * ビュー ボディが行ベース レプリケーションを要求し、そのビューを作成しているステートメントがそれを使用するとき — たとえば、ビューを作成しているステートメントが UUID() 関数を使用するとき
  * UDF の呼び出しに関わるとき
* MySQL 5.1.12 から、ミックス ベース レプリケーション (MBR) がデフォルト フォーマットで、指定のない限り、すべてのレプリケーション環境に対応します。  
http://dev.mysql.com/doc/refman/5.1/ja/replication-formats.html より

## 設定

* MySQL で実行された SQL をログに記録
  ```
  vi /etc/my.cnf
  log=/var/log/mysqld-query.log
  touch /var/log/mysqld-query.log
  chown mysql.mysql /var/log/mysqld-query.log
  chmod 640 /var/log/mysqld-query.log
  /sbin/service mysqld restart
  ```

### スキーマ作成

* データベース作成  
create database <データベース名> CHARACTER SET utf8;
* ユーザ作成と権限付与  
grant all on <データベース名>.* to <ユーザ>@<接続許可ホスト> identified by '<パスワード>';  
(例)  
grant all on mydb.* to userfoo@localhost identified by 'password';  
※データベース名を * にすると、すべてのデータベースに対する権限が与えられます。  
※@ の後に続くホスト名に ”%” を指定すると、すべてのホストから接続することができます。  
(% をダブルクォートで囲む必要があります。)  
192.168.1.0/24 からだけ接続できるようにする場合は “192.168.1.%” と指定します。  
* テーブル作成  
create table <テーブル名> (id integer);
* テーブル作成(DBエンジン指定あり)  
create table <テーブル名> (id integer) type=InnoDB;
* テーブル作成(PK と DBエンジンを指定)  
create table <テーブル名> (id integer, name varchar(16) constraint PK_XXX primary key (id)) type=InnoDB;
* DBエンジンを後から指定  
alter table <テーブル名> type=InnoDB;
* ユニークインデックス作成  
create unique index <インデックス名> on <テーブル名> (id1, id2);
* 重複インデックス作成  
create index <インデックス名> on <テーブル名> (id1, id2);
* カラム定義を変更  
alter table <テーブル名> change <変更前カラム名> <変更後カラム定義>;  
(例)  
alter table account change name name varchar(32) not null;

### 管理

#### バックアップ

* ダンプ  
mysqldump -u <user> -p<password> -x –all-databases | gzip > / tmp/mysql.dump.gz
* 復元  
mysql -u <root> -p<password> < dumpdata  
または  
gzip -dc /tmp/mysql.dump.gz | mysql -u <root> -p<password>
* 別ホストのデータベースを移行  
別ホストへの接続が許可されている場合  
`mysqldump -h 192.168.1.1 -u root -p -x --all-databases | mysql -u root`  
別ホストへの接続が許可されていない場合  
`ssh root@192.168.1.1 mysqldump -u root -p<password> -x --all-databases | mysql -u root`
* スナップショット  
mysqlsnapshot -u <user> -p<password> -s /tmp –split -n
* ホットコピー  
mysqlhotcopy -u <user> -p<password> <データベース名> /tmp

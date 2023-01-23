# Oracle


### Tips

#### listener.ora の HOST に localhost を指定する方法

localhost で動的サービス登録が行えるよう初期パラメータを設定 (Oracle 10g XE で確認)

1. spfile から pfile を取得
   * sqlplus / as sysdba
   * create pfile='/usr/lib/oracle/xe/app/oracle/product/10.2.0/server/dbs/initSJIS.ora' from spfile='/usr/lib/oracle/xe/app/oracle/product/10.2.0/server/dbs/spfileSJIS.ora';
   * exit
1. pfile を編集
   * vi /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/dbs/initSJIS.ora
   * 次の行を追加  
.LOCAL_LISTENER=”(ADDRESS=(PROTOCOL=TCP)(HOST=localhost)(PORT=1521))”
1. pfile から spfile を作成
   * sqlplus / as sysdba
   * create spfile from pfile;

### 管理

#### SQL などの実行履歴を監査(ファイル出力)
```
ALTER SYSTEM SET AUDIT_TRAIL=OS SCOPE=PFILE;
ALTER SYSTEM SET AUDIT_FILE_DEST='/var/log/oracle' SCOPE=PFILE;
AUDIT_FILE_DEST はディレクトリを指定。
```

#### SQL 実行トレースを出力
```
ALTER SYSTEM SET SQL_TRACE=TRUE SCOPE=PFILE;
```

#### 元に戻す
```
ALTER SYSTEM SET AUDIT_TRAIL=NONE SCOPE=PFILE;
ALTER SYSTEM SET SQL_TRACE=FALSE SCOPE=PFILE;
```

#### SQLトレースの取得

* SYSユーザで接続
  ```
  $ sqlplus /nolog
      SQL> conn sys/xxxx as sysdba
  ```
* 対象セッション確認
  ```
  SQL> SELECT sid,serial#,username,program,machine,status FROM v$session;
  ```
* トレース開始
  ```
  SQL> EXECUTE DBMS_SYSTEM.SET_SQL_TRACE_IN_SESSION([sid],[serial#],TRUE);
  ```
  udumpディレクトリ配下のトレースファイルを参照。
* トレース終了
  ```
  SQL> EXECUTE DBMS_SYSTEM.SET_SQL_TRACE_IN_SESSION([sid],[serial#],FALSE);
  ```

### HTML から移行(未分類)

#### ユーザー SYS のデフォルトパスワード
```
CHANGE_ON_INSTALL
```

#### パブリックシノニム 'foo' を作成する
```
create public synonym foo for sys.foo;
```

#### 作成したシノニム 'foo' に対して実行権限を与える(PROCEDURE, FUNCTION, PACKAGE)
```
grant execute on synonym foo for public
```

#### SQL Loader を使う

1. control ファイルを作成
   ```
   > type tablename.ctl
   LOAD DATA APPEND INTO TABLE tablename FIELDS TERMINATED BY ' ' ( fieldname1, fieldname2 char(1024),fieldname3 DATE 'YYMMDD' )
   ```
1. SQL Loader 実行
   ```
   sqlldr userid/password@connstring control=tablename.ctl data=tablename.txt
   ```
1. SQL Loader オプション  
sqlldr -help
   ```
   userid  ORACLE username/password
   control   Control file name
   log   Log file name
   bad   Bad file name
   data  Data file name
   discard   Discard file name
   discardmax  Number of discards to allow
   skip  Number of logical records to skip
   load  Number of logical records to load
   errors  Number of errors to allow
   rows  Number of rows in conventional path bind array or between direct path data saves
   bindsize  Size of conventional path bind array in bytes
   silent  Suppress messages during run(header,feedback,errors,discards,partitions)
   direct  use direct path
   _synchro  internal testing
   parfile   parameter file: name of file that contains parameter specifications
   parallel  do parallel load
   file  File to allocate extents from
   skip_unusable_indexes   disallow/allow unusable indexes or index partitions
   skip_index_maintenance  do not maintain indexes(mark affected indexes as unusable)
   commit_discontinued   commit loaded rows when load is discontinued
   _display_exitcode   Display exit code for SQL*Loader execution
   readsize  Size of Read buffer
   ```

#### import オプション

`imp -help`

```
  IMPを入力するとインポートがパラメータ入力を要求します。
  コマンドに続いてユーザー名/パスワードを指定してください。

       入力例: IMP SCOTT/TIGER

  また、IMPコマンドの後に引数を入力することによって、インポートの実行を
  制御することができます。パラメータの指定にはキーワードを使用します。

       形式  : IMP KEYWORD=value または KEYWORD=(value1,value2,...,valueN)
       入力例: IMP SCOTT/TIGER IGNORE=Y TABLES=(EMP,DEPT) FULL=N
                 またはTABLES=(T1:P1,T1:P2)、ただしT1がパーティション化された表の
  場合。

  USERIDは、コマンド行の最初のパラメータでなければなりません。

  キーワード  内容(デフォルト値)        キーワード   内容(デフォルト値)
  --------------------------------------------------------------------------
  USERID      ユーザー名/パスワード     FULL         全ファイルをインポート(N)
  BUFFER      データ・バッファ・サイズ  FROMUSER     所有するユーザーのリスト
  FILE        入力ファイル(EXPDAT.DMP)  TOUSER       ユーザー名リスト
  SHOW        EXPファイル内容表示(N)    TABLES       表名リスト
  IGNORE      作成時エラー無視(N)       RECORDLENGTH ファイルのIOレコード長
  GRANTS      権限のインポート(Y)       INCTYPE      増分インポートの種類
  INDEXES     索引のインポート(Y)       COMMIT       配列挿入後にコミット(N)
  ROWS        表データ行のインポート(Y)   PARFILE    パラメータ・ファイル
  LOG     　　スクリーン出力のログ・ファイル  CONSTRAINTS  制約のインポート(Y)
  DESTROY     表領域データ・ファイルの上書き(N)
  INDEXFILE   指定されたファイルへ表/索引情報の書込み
  SKIP_UNUSABLE_INDEXES  使用不能な索引のメンテナンスをスキップ(N)
  ANALYZE     ダンプ・ファイルでANALYZE文を実行(Y)
  FEEDBACK    x行ごとに進捗を表示(0)
  TOID_NOVALIDATE  指定した型のID妥当性チェックをスキップ
  FILESIZE    各ダンプ・ファイルの最大サイズ
  RECALCULATE_STATISTICS 統計情報を再計算(N)

  次のキーワードは、トランスポータブル表領域だけに適用されます。
  TRANSPORT_TABLESPACEは、インポートするトランスポータブル表領域メタデータ(N)。
  TABLESPACESは、データベース内に移送される表領域。
  DATAFILESは、データベース内に移送されるデータファイル。
  TTS_OWNERSは、トランスポータブル表領域セット内にデータを所有するユーザー。
```

#### export オプション

`exp -help`

```
  EXPを入力するとエクスポートがパラメータ入力を要求します。
  コマンドに続いてユーザー名/パスワードを指定してください。

       入力例: EXP SCOTT/TIGER

  また、EXPコマンド後に引数を入力することによって、エクスポートの実行を
  制御することができます。パラメータの指定にはキーワードを使用します。

       形式  : EXP KEYWORD=value or KEYWORD=(value1,value2,...,valueN)
       入力例: EXP SCOTT/TIGER GRANTS=Y TABLES=(EMP,DEPT,MGR)
                 またはTABLES=(T1:P1,T1:P2)、ただしT1がパーティション化された表の
  場合。

  USERIDは、コマンド行の最初のパラメータでなければなりません。

  キーワード  内容(デフォルト値)        キーワード   内容(デフォルト値)
  --------------------------------------------------------------------------
  USERID      ユーザー名/パスワード     FULL         全エクスポート・モード(N)
  BUFFER      データ・バッファ・サイズ  OWNER        所有するユーザー・リスト
  FILE        出力ファイル(EXPDAT.DMP)  TABLES       表名のリスト
  COMPRESS    エクステント圧縮(Y)       RECORDLENGTH ファイルのレコード長
  GRANTS      権限のEXPORT(Y)           INCTYPE      増分EXPORTの種類
  INDEXES     索引のEXPORT(Y)           RECORD       増分EXPORTをDB表に記録(Y)
  ROWS        表データ行のEXPORT(Y)     PARFILE      パラメータ・ファイル
  CONSTRAINTS 表に対する制約のEXPORT(Y) CONSISTENT   表相互の一貫性 (Y)
  LOG         画面出力のログ・ファイル  STATISTICS   オブジェクトを分析(ESTIMATE)
  DIRECT      ダイレクト・パス(N)       TRIGGERS     エクスポート・トリガー(Y)
  FEEDBACK    x行ごとに進捗を表示(0)
  FILESIZE    各ダンプ・ファイルの最大サイズ
  QUERY       表のサブセットをエクスポートするために使用するSELECT句

  次のキーワードは、トランスポータブル表領域だけに適用されます。
  TRANSPORT_TABLESPACEは、エクスポートするトランスポータブル表領域メタデータ(N)。
  TABLESPACESは、移送する表領域のリスト。
```

#### CSVファイルを作成する

```
set echo off set
heading off
set termout off
set pause off
set pagesize 0
set linesize 80
set feedback off
spool emp.csv
select empno || ',' || ename || ',' || 2 job || ',' || sal from emp; spool off
```

#### 接続ユーザーを表示
````
col username format a12
col osuser format a20
col program format a20
col machine format a20
select username,osuser,program,machine from v$session where username is not null;
````

#### データベースの CHARSET を表示
```
col value format a28
col parameter format a28
select * from nls_database_parameters;
```

#### テーブルスペース

##### テーブルスペース作成
```
create tablespace TablespaceName
datafile 'd:\dir\TablespaceName.dbf'
size 8m reuse autoextend
on next 320k minimum extent 32k maximum 10m
default storage (initial 32k next 32k minextents 1 maxextents unlimited pctincrease 0);
```

表領域を無制限に拡大する場合は、autoextend 句に unlimited を指定

##### テーブルスペースの自動拡張を有効に設定。

```
ALTER DATABASE DATAFILE 'd:\oracle\data\xxx.dbf' AUTOEXTEND ON;
```

##### テーブルスペース一覧表示
```
col file_name format a59
col tablespace_name format a20
select tablespace_name,file_name from dba_data_files;
```

##### テーブルスペースの状態を表示
```
select tablespace_name,status from dba_tablespaces;
```

##### テーブルスペースの空き容量を表示
```
select tablespace_name, sum(bytes)/1024/1024 from dba_free_space group by tablespace_name;
```

##### テーブルスペースの状態を表示
```
col tablespace_name format a10
select tablespace_name,file_id, count(*) "Pieces",
max(blocks) "Maximum",
min(blocks) "Minimum",
avg(blocks) "Average",
sum(blocks) "Total"
from dba_free_space group by tablespace_name,file_id
order by tablespace_name,file_id;
```

##### テーブルスペースの EXTENT を表示
```
col space format a10
select tablespace_name "SPACE",
initial_extent "INITIAL",
next_extent "NEXT",
min_extents "MIN",
max_extents "MAX",
pct_increase "INCREASE",
min_extlen "MIN_EXT"
from dba_tablespaces;
```

##### テーブルスペース表示用ビュー作成(SYSで行う)
```
create view V_FREESPACE as
select tablespace_name as tablespace,
file_id as id,
count(*) as pieces,
sum(bytes) as byte
from dba_free_space group by tablespace_name,file_id
with read only; grant select on v_freespace to public;
```

##### テーブルスペースの使用状況を表示
```
select f.tablespace "Tablespace",
round(sum(d.bytes)/1048576) "Total MB",
round((sum(d.bytes)-sum(f.byte))/1048576) "Used MB",
round(sum(f.byte)/1048576) "Free MB",
round((sum(d.bytes)-sum(f.byte))/sum(d.bytes)*100,1) "Used %"
from sys.v_freespace f, dba_data_files d where f.id=d.file_id group by f.tablespace order by f.tablespace;
```

##### テーブルスペース割当て状況表示
```
col tablespace format a10
col "file name" format a50
select d.tablespace_name "Tablespace", round(sum(d.bytes)/1048576) "Alloc MB",
sum(f.pieces) "Pieces",
d.file_name "File Name"
from dba_data_files d, sys.v_freespace f where d.file_id=f.id
group by d.tablespace_name,d.file_name order by d.tablespace_name,d.file_name;
```
```
select tablespace_name,bytes/1000000 as
Mbyetes,autoextensible,maxbytes/1000000 as max,user_bytes/1000000
as use from dba_data_Files;
```

##### 物理ファイルごとテーブルスペース削除
```
DROP TABLESPACE tablespace_name INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;
```

#### テーブル

##### テーブルエクステント表示
```
col tablespace_name format a22
col segment_name format a22
col segment_type format a8
select tablespace_name, segment_name, segment_type, count(*)
from dba_extents group by tablespace_name, segment_name, segment_type having count(*) > 2;
```

##### エクステントが発生したテーブルを表示
```
col tablespace_name format a22
col segment_name format a22
col segment_type format a8
select tablespace_name, segment_name, segment_type, count(*) from dba_extents where owner='owner'
group by tablespace_name, segment_name, segment_type having count(*) > 2;
```

##### エクステント,バイト表示(テーブル)
```
col tablespace_name format a22
col segment_name format a22
col segment_type format a8
select tablespace_name, segment_name, segment_type, sum(bytes), count(*)
from dba_extents where owner='owner' and segment_type='TABLE' group by tablespace_name, segment_name, segment_type;
```

##### エクステント,バイト表示(インデックス)
```
col tablespace_name format a22
col segment_name format a22
col segment_type format a8
select tablespace_name, segment_name, segment_type, sum(bytes), count(*) from dba_extents where owner='owner' and segment_type='INDEX' group by tablespace_name, segment_name, segment_type;
```

#### ユーザ

##### ユーザー一覧表示
```
select name from sys.user$ where type#=1;
```

##### 新規ユーザー作成
```
create user newuser
identified by newpassword
default tablespace tablespacename
temporary tablespace tempporarytablespacename;
```

##### 接続許可を与える。
```
grant connect, resource to newuser;
```

##### DBA権限を与える
```
grant dba to newuser;
```

##### テーブルスペースを制限する
```
revoke unlimited tablespace from newuser;
```

##### テーブルスペースを制限しない
```
alter user newuser quota unlimited on tablespacename;
```

##### システム領域の上限を10MBに設定する
```
alter user newuser quota 10m on system;
```

##### パスワード変更
```
alter user <user> identified by <password>;
```

##### テーブルと作成した表領域を一覧表示

該当するユーザーで接続して
```
col segment_name format a40
col segment_type format a8
col tablespace_name format a10
select segment_name,segment_type,tablespace_name
from user_segments where segment_type='TABLE';
```

または

```
select table_name from tabs;
```

##### 一般ユーザーが SYSTEM領域に作成したテーブルを表示
```
select segment_name,segment_type,tablespace_name from user_segments where tablespace_name='SYSTEM';
```

### PL/SQL

#### PL/SQL を一覧表示

##### プロシージャ一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='PROCEDURE';
```

##### ファンクション一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='FUNCTION';
```

##### パッケージ宣言部一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='PACKAGE';
```

##### パッケージ仕様部一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='PACKAGE BODY';
```

### (参考) インデックス一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='INDEX';
```

### テーブル一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='TABLE';
```

### シーケンス一覧
```
SELECT OBJECT_NAME,STATUS FROM USER_OBJECTS WHERE OBJECT_TYPE='SEQUENCE';
```

### ロールバックセグメント

#### ロールバックセグメントの状態を表示
```
select segment_name,status from dba_rollback_segs;
```

#### ロールバックセグメントをオンラインにする
```
alter rollback segment rbs0 online;
```

#### 起動時にオンラインにする
```
> grep rollback_segments c:\Oracle\admin\ora816\pfile\init.ora
rollback_segments = ( RBS0, RBS1, RBS2, RBS3, RBS4, RBS5, RBS6 )
```

### Primary Key

#### tablename の Primary Key の項目を表示
```
select a.table_name,a.constraint_name,a.column_name
  from user_cons_columns a,user_constraints b where a.constraint_name=b.constraint_name and b.constraint_type='P' and a.table_name='tablename';
```

または

```
select constraint_name,table_name,column_name
  from user_cons_columns where table_name='tablename';
```

#### Primary Key を削除
```
alter table tablename drop primary key;
```

### Index

#### tablenameのインデックスを一覧表示
```
select index_name from user_indexes where table_name='tablename';
```

#### インデックスのカラムを一覧表示
```
select * from user_ind_columns where index_name='indexname';
```

#### tablenameのインデックスとカラムを一覧表示
```
select c.index_name,c.column_name
  from user_ind_columns c,user_indexes i
  where c.index_name=i.index_name and i.table_name='table_name'
  order by c.index_name;
```

#### すべてのテーブルのインデックス一覧を表示
```
column TBNAME format A15 heading 'TABLE NAME'
column IDNAME format A24 heading 'INDEX NAME'
column CLNAME format A20 heading 'COLUMN NAME'
select c.table_name, c.index_name,c.column_name
from user_ind_columns c,user_indexes i
where c.index_name=i.index_name
order by c.index_name;
```

#### インデックス削除
```
alter table tablename drop constraint index_name;
drop index index_name;
```

### テーブルのデータを削除(ロールバックのログをとらずに)
```
truncate table tablename;
```

### edit で起動されるエディタを秀丸に設定
```
define_editor="c:\program files\hidemaru\hidemaru.exe"
```

### Package

#### package の宣言部を一覧表示
```
select text from user_source where name='package' and type='PACKAGE'
  order by line;
```

#### package の仕様部を一覧表示
```
select text from user_source where name='package' and type='PACKAGE BODY'
  order by line;
```

### PL/SQL から変数 foo の内容を表示
```
set serveroutput on
```
または
```
dbms_output.put_line(foo);
```

### バッファサイズを 2000バイトに設定, 設定可能値:2000～1,000,000
```
set serveroutput on size 2000
```

### Tips

#### カラム名変更
```
update SYS.COL$ c set c.NAME = 'NEW_COLUMN_NAME' where c.NAME = 'OLD_COLUMN_NAME' and c.OBJ# in ( select o.OBJ# from SYS.OBJ$ o, SYS.USER$ u where o.OWNER# = u.USER# and u.NAME = 'TABLE_OWNER' and o.NAME = 'TABLE_NAME' ) ;
commit; alter system flush shared_pool;
```

#### Windows 上の Oracle で TCP の接続ポートを固定。
```
REGEDIT4

[HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE\HOME0]
"USE_SHARED_SOCKET"=hex(2):54,52,55,45,00
```
をレジストリに指定。

#### Oracle 8i を Windows 2000 環境にセットアップし、SQL Plus で接続すると、「ORA-27101: shared memory realm does not exist」というメッセージが表示される。

%ORACLE_HOME%\network\ADMIN\sqlnet.ora の
```
SQLNET.AUTHENTICATION_SERVICES= (NTS)
```
という行をコメントにする。または
```
SQLNET.AUTHENTICATION_SERVICES= (NONE)
```
に変更する。

#### create tablespace を実行すると「ORA-01144: ファイル・サイズ(4352000ブロック)が最大値: 4194303ブロックを超えています。」というメッセージが表示される。

表領域の最大サイズは init.ora に記述されている db_block_size * 4194303 であるため、db_block_size に指定されているブロックサイズを大きくする。

### Oracle 9i を Linux 環境にセットアップ。

root 権限で下記処理を実行。

1. X を起動。
1. コンソールを開く。
1. export DISPLAY=127.0.0.1:0.0
1. xhost +
1. ./runInstaller

次の環境変数を設定。
```
ORACLE_HOME=/opt/oracle/OraHome1
ORACLE_SID=ORCL
ORAHOME=/opt/oracle/OraHome1
ORASID=ORCL
NLS_LANG=JAPANESE_JAPAN.JA16EUC
```

### ユーザごとのオブジェクト

```
USERM_PK  INDEX
USERTABLE_PKEY  INDEX
USERTABLE_PKEY  INDEX
USER_ALL_TABLES   SYNONYM
USER_ARGUMENTS  SYNONYM
USER_ASSOCIATIONS   SYNONYM
USER_AUDIT_OBJECT   SYNONYM
USER_AUDIT_SESSION  SYNONYM
USER_AUDIT_STATEMENT  SYNONYM
USER_AUDIT_TRAIL  SYNONYM
USER_CATALOG  SYNONYM
USER_CLUSTERS   SYNONYM
USER_CLUSTER_HASH_EXPRESSIONS   SYNONYM
USER_CLU_COLUMNS  SYNONYM
USER_COLL_TYPES   SYNONYM
USER_COL_COMMENTS   SYNONYM
USER_COL_PRIVS  SYNONYM
USER_COL_PRIVS_MADE   SYNONYM
USER_COL_PRIVS_RECD   SYNONYM
USER_CONSTRAINTS  SYNONYM
USER_CONS_COLUMNS   SYNONYM
USER_DB_LINKS   SYNONYM
USER_DEPENDENCIES   SYNONYM
USER_DIMENSIONS   SYNONYM
USER_DIM_ATTRIBUTES   SYNONYM
USER_DIM_CHILD_OF   SYNONYM
USER_DIM_HIERARCHIES  SYNONYM
USER_DIM_JOIN_KEY   SYNONYM
USER_DIM_LEVELS   SYNONYM
USER_DIM_LEVEL_KEY  SYNONYM
USER_ERRORS   SYNONYM
USER_EXTENTS  SYNONYM
USER_FREE_SPACE   SYNONYM
USER_GEOMETRY_COLUMNS   SYNONYM
USER_HISTOGRAMS   SYNONYM
USER_INDEXES  SYNONYM
USER_INDEXTYPES   SYNONYM
USER_INDEXTYPE_OPERATORS  SYNONYM
USER_IND_COLUMNS  SYNONYM
USER_IND_EXPRESSIONS  SYNONYM
USER_IND_PARTITIONS   SYNONYM
USER_IND_SUBPARTITIONS  SYNONYM
USER_INTERNAL_TRIGGERS  SYNONYM
USER_JAVA_POLICY  SYNONYM
USER_JOBS   SYNONYM
USER_LIBRARIES  SYNONYM
USER_LOBS   SYNONYM
USER_LOB_PARTITIONS   SYNONYM
USER_LOB_SUBPARTITIONS  SYNONYM
USER_MD_COLUMNS   SYNONYM
USER_MD_DIMENSIONS  SYNONYM
USER_MD_EXCEPTIONS  SYNONYM
USER_MD_LOADER_ERRORS   SYNONYM
USER_MD_PARTITIONS  SYNONYM
USER_MD_TABLES  SYNONYM
USER_MD_TABLESPACES   SYNONYM
USER_METHOD_PARAMS  SYNONYM
USER_METHOD_RESULTS   SYNONYM
USER_MVIEWS   SYNONYM
USER_MVIEW_AGGREGATES   SYNONYM
USER_MVIEW_ANALYSIS   SYNONYM
USER_MVIEW_DETAIL_RELATIONS   SYNONYM
USER_MVIEW_JOINS  SYNONYM
USER_MVIEW_KEYS   SYNONYM
USER_NESTED_TABLES  SYNONYM
USER_OBJECTS  SYNONYM
USER_OBJECT_SIZE  SYNONYM
USER_OBJECT_TABLES  SYNONYM
USER_OBJ_AUDIT_OPTS   SYNONYM
USER_OPANCILLARY  SYNONYM
USER_OPARGUMENTS  SYNONYM
USER_OPBINDINGS   SYNONYM
USER_OPERATORS  SYNONYM
USER_OUTLINES   SYNONYM
USER_OUTLINE_HINTS  SYNONYM
USER_PARTIAL_DROP_TABS  SYNONYM
USER_PART_COL_STATISTICS  SYNONYM
USER_PART_HISTOGRAMS  SYNONYM
USER_PART_INDEXES   SYNONYM
USER_PART_KEY_COLUMNS   SYNONYM
USER_PART_LOBS  SYNONYM
USER_PART_TABLES  SYNONYM
USER_PASSWORD_LIMITS  SYNONYM
USER_POLICIES   SYNONYM
USER_QUEUES   SYNONYM
USER_QUEUE_SCHEDULES  SYNONYM
USER_QUEUE_TABLES   SYNONYM
USER_REFRESH  SYNONYM
USER_REFRESH_CHILDREN   SYNONYM
USER_REFS   SYNONYM
USER_REGISTERED_SNAPSHOTS   SYNONYM
USER_REPAUDIT_ATTRIBUTE   SYNONYM
USER_REPAUDIT_COLUMN  SYNONYM
USER_REPCAT   SYNONYM
USER_REPCATLOG  SYNONYM
USER_REPCAT_REFRESH_TEMPLATES   SYNONYM
USER_REPCAT_TEMPLATE_OBJECTS  SYNONYM
USER_REPCAT_TEMPLATE_PARMS  SYNONYM
USER_REPCAT_TEMPLATE_SITES  SYNONYM
USER_REPCAT_USER_AUTHORIZATION  SYNONYM
USER_REPCAT_USER_PARM_VALUES  SYNONYM
USER_REPCOLUMN  SYNONYM
USER_REPCOLUMN_GROUP  SYNONYM
USER_REPCONFLICT  SYNONYM
USER_REPDDL   SYNONYM
USER_REPFLAVORS   SYNONYM
USER_REPFLAVOR_COLUMNS  SYNONYM
USER_REPFLAVOR_OBJECTS  SYNONYM
USER_REPGENERATED   SYNONYM
USER_REPGENOBJECTS  SYNONYM
USER_REPGROUP   SYNONYM
USER_REPGROUPED_COLUMN  SYNONYM
USER_REPGROUP_PRIVILEGES  SYNONYM
USER_REPKEY_COLUMNS   SYNONYM
USER_REPOBJECT  SYNONYM
USER_REPPARAMETER_COLUMN  SYNONYM
USER_REPPRIORITY  SYNONYM
USER_REPPRIORITY_GROUP  SYNONYM
USER_REPPROP  SYNONYM
USER_REPRESOLUTION  SYNONYM
USER_REPRESOLUTION_METHOD   SYNONYM
USER_REPRESOLUTION_STATISTICS   SYNONYM
USER_REPRESOL_STATS_CONTROL   SYNONYM
USER_REPSCHEMA  SYNONYM
USER_REPSITES   SYNONYM
USER_RESOURCE_LIMITS  SYNONYM
USER_ROLE_PRIVS   SYNONYM
USER_RSRC_CONSUMER_GROUP_PRIVS  SYNONYM
USER_RSRC_MANAGER_SYSTEM_PRIVS  SYNONYM
USER_RULESETS   SYNONYM
USER_SDO_GEOM_METADATA  SYNONYM
USER_SDO_INDEX_METADATA   SYNONYM
USER_SEGMENTS   SYNONYM
USER_SEQUENCES  SYNONYM
USER_SNAPSHOTS  SYNONYM
USER_SNAPSHOT_LOGS  SYNONYM
USER_SNAPSHOT_REFRESH_TIMES   SYNONYM
USER_SOURCE   SYNONYM
USER_SUBPART_COL_STATISTICS   SYNONYM
USER_SUBPART_HISTOGRAMS   SYNONYM
USER_SUBPART_KEY_COLUMNS  SYNONYM
USER_SUMMARIES  SYNONYM
USER_SUMMARY_AGGREGATES   SYNONYM
USER_SUMMARY_DETAIL_TABLES  SYNONYM
USER_SUMMARY_JOINS  SYNONYM
USER_SUMMARY_KEYS   SYNONYM
USER_SYNONYMS   SYNONYM
USER_SYS_PRIVS  SYNONYM
USER_TABLES   SYNONYM
USER_TABLESPACES  SYNONYM
USER_TAB_COLUMNS  SYNONYM
USER_TAB_COL_STATISTICS   SYNONYM
USER_TAB_COMMENTS   SYNONYM
USER_TAB_HISTOGRAMS   SYNONYM
USER_TAB_MODIFICATIONS  SYNONYM
USER_TAB_PARTITIONS   SYNONYM
USER_TAB_PRIVS  SYNONYM
USER_TAB_PRIVS_MADE   SYNONYM
USER_TAB_PRIVS_RECD   SYNONYM
USER_TAB_SUBPARTITIONS  SYNONYM
USER_TIMESERIES_COLS  SYNONYM
USER_TIMESERIES_GROUPS  SYNONYM
USER_TIMESERIES_OBJS  SYNONYM
USER_TRIGGERS   SYNONYM
USER_TRIGGER_COLS   SYNONYM
USER_TS_QUOTAS  SYNONYM
USER_TYPES  SYNONYM
USER_TYPE_ATTRS   SYNONYM
USER_TYPE_METHODS   SYNONYM
USER_UNUSED_COL_TABS  SYNONYM
USER_UPDATABLE_COLUMNS  SYNONYM
USER_USERS  SYNONYM
USER_USTATS   SYNONYM
USER_VARRAYS  SYNONYM
USER_VIEWS  SYNONYM
USER_ALL_TABLES   VIEW
USER_ARGUMENTS  VIEW
USER_ASSOCIATIONS   VIEW
USER_AUDIT_OBJECT   VIEW
USER_AUDIT_SESSION  VIEW
USER_AUDIT_STATEMENT  VIEW
USER_AUDIT_TRAIL  VIEW
USER_CATALOG  VIEW
USER_CLUSTERS   VIEW
USER_CLUSTER_HASH_EXPRESSIONS   VIEW
USER_CLU_COLUMNS  VIEW
USER_COLL_TYPES   VIEW
USER_COL_COMMENTS   VIEW
USER_COL_PRIVS  VIEW
USER_COL_PRIVS_MADE   VIEW
USER_COL_PRIVS_RECD   VIEW
USER_CONSTRAINTS  VIEW
USER_CONS_COLUMNS   VIEW
USER_DB_LINKS   VIEW
USER_DEPENDENCIES   VIEW
USER_DIMENSIONS   VIEW
USER_DIM_ATTRIBUTES   VIEW
USER_DIM_CHILD_OF   VIEW
USER_DIM_HIERARCHIES  VIEW
USER_DIM_JOIN_KEY   VIEW
USER_DIM_LEVELS   VIEW
USER_DIM_LEVEL_KEY  VIEW
USER_ERRORS   VIEW
USER_EXTENTS  VIEW
USER_FREE_SPACE   VIEW
USER_GEOMETRY_COLUMNS   VIEW
USER_INDEXES  VIEW
USER_INDEXTYPES   VIEW
USER_INDEXTYPE_OPERATORS  VIEW
USER_IND_COLUMNS  VIEW
USER_IND_EXPRESSIONS  VIEW
USER_IND_PARTITIONS   VIEW
USER_IND_SUBPARTITIONS  VIEW
USER_INTERNAL_TRIGGERS  VIEW
USER_JAVA_POLICY  VIEW
USER_JOBS   VIEW
USER_LIBRARIES  VIEW
USER_LOBS   VIEW
USER_LOB_PARTITIONS   VIEW
USER_LOB_SUBPARTITIONS  VIEW
USER_MD_COLUMNS   VIEW
USER_MD_DIMENSIONS  VIEW
USER_MD_EXCEPTIONS  VIEW
USER_MD_LOADER_ERRORS   VIEW
USER_MD_PARTITIONS  VIEW
USER_MD_TABLES  VIEW
USER_MD_TABLESPACES   VIEW
USER_METHOD_PARAMS  VIEW
USER_METHOD_RESULTS   VIEW
USER_MVIEWS   VIEW
USER_MVIEW_AGGREGATES   VIEW
USER_MVIEW_ANALYSIS   VIEW
USER_MVIEW_DETAIL_RELATIONS   VIEW
USER_MVIEW_JOINS  VIEW
USER_MVIEW_KEYS   VIEW
USER_NESTED_TABLES  VIEW
USER_OBJECTS  VIEW
USER_OBJECT_SIZE  VIEW
USER_OBJECT_TABLES  VIEW
USER_OBJ_AUDIT_OPTS   VIEW
USER_OPANCILLARY  VIEW
USER_OPARGUMENTS  VIEW
USER_OPBINDINGS   VIEW
USER_OPERATORS  VIEW
USER_OUTLINES   VIEW
USER_OUTLINE_HINTS  VIEW
USER_PARTIAL_DROP_TABS  VIEW
USER_PART_COL_STATISTICS  VIEW
USER_PART_HISTOGRAMS  VIEW
USER_PART_INDEXES   VIEW
USER_PART_KEY_COLUMNS   VIEW
USER_PART_LOBS  VIEW
USER_PART_TABLES  VIEW
USER_PASSWORD_LIMITS  VIEW
USER_POLICIES   VIEW
USER_QUEUES   VIEW
USER_QUEUE_SCHEDULES  VIEW
USER_QUEUE_TABLES   VIEW
USER_REFRESH  VIEW
USER_REFRESH_CHILDREN   VIEW
USER_REFS   VIEW
USER_REGISTERED_SNAPSHOTS   VIEW
USER_REPAUDIT_ATTRIBUTE   VIEW
USER_REPAUDIT_COLUMN  VIEW
USER_REPCAT   VIEW
USER_REPCATLOG  VIEW
USER_REPCAT_REFRESH_TEMPLATES   VIEW
USER_REPCAT_TEMPLATE_OBJECTS  VIEW
USER_REPCAT_TEMPLATE_PARMS  VIEW
USER_REPCAT_TEMPLATE_SITES  VIEW
USER_REPCAT_USER_AUTHORIZATION  VIEW
USER_REPCAT_USER_PARM_VALUES  VIEW
USER_REPCOLUMN  VIEW
USER_REPCOLUMN_GROUP  VIEW
USER_REPCONFLICT  VIEW
USER_REPDDL   VIEW
USER_REPFLAVORS   VIEW
USER_REPFLAVOR_COLUMNS  VIEW
USER_REPFLAVOR_OBJECTS  VIEW
USER_REPGENERATED   VIEW
USER_REPGENOBJECTS  VIEW
USER_REPGROUP   VIEW
USER_REPGROUPED_COLUMN  VIEW
USER_REPGROUP_PRIVILEGES  VIEW
USER_REPKEY_COLUMNS   VIEW
USER_REPOBJECT  VIEW
USER_REPPARAMETER_COLUMN  VIEW
USER_REPPRIORITY  VIEW
USER_REPPRIORITY_GROUP  VIEW
USER_REPPROP  VIEW
USER_REPRESOLUTION  VIEW
USER_REPRESOLUTION_METHOD   VIEW
USER_REPRESOLUTION_STATISTICS   VIEW
USER_REPRESOL_STATS_CONTROL   VIEW
USER_REPSCHEMA  VIEW
USER_REPSITES   VIEW
USER_RESOURCE_LIMITS  VIEW
USER_ROLE_PRIVS   VIEW
USER_RSRC_CONSUMER_GROUP_PRIVS  VIEW
USER_RSRC_MANAGER_SYSTEM_PRIVS  VIEW
USER_RULESETS   VIEW
USER_SDO_GEOM_METADATA  VIEW
USER_SDO_INDEX_METADATA   VIEW
USER_SEGMENTS   VIEW
USER_SEQUENCES  VIEW
USER_SNAPSHOTS  VIEW
USER_SNAPSHOT_LOGS  VIEW
USER_SNAPSHOT_REFRESH_TIMES   VIEW
USER_SOURCE   VIEW
USER_SUBPART_COL_STATISTICS   VIEW
USER_SUBPART_HISTOGRAMS   VIEW
USER_SUBPART_KEY_COLUMNS  VIEW
USER_SUMMARIES  VIEW
USER_SUMMARY_AGGREGATES   VIEW
USER_SUMMARY_DETAIL_TABLES  VIEW
USER_SUMMARY_JOINS  VIEW
USER_SUMMARY_KEYS   VIEW
USER_SYNONYMS   VIEW
USER_SYS_PRIVS  VIEW
USER_TABLES   VIEW
USER_TABLESPACES  VIEW
USER_TAB_COLUMNS  VIEW
USER_TAB_COL_STATISTICS   VIEW
USER_TAB_COMMENTS   VIEW
USER_TAB_HISTOGRAMS   VIEW
USER_TAB_MODIFICATIONS  VIEW
USER_TAB_PARTITIONS   VIEW
USER_TAB_PRIVS  VIEW
USER_TAB_PRIVS_MADE   VIEW
USER_TAB_PRIVS_RECD   VIEW
USER_TAB_SUBPARTITIONS  VIEW
USER_TIMESERIES_COLS  VIEW
USER_TIMESERIES_GROUPS  VIEW
USER_TIMESERIES_OBJS  VIEW
USER_TRIGGERS   VIEW
USER_TRIGGER_COLS   VIEW
USER_TS_QUOTAS  VIEW
USER_TYPES  VIEW
USER_TYPE_ATTRS   VIEW
USER_TYPE_METHODS   VIEW
USER_UNUSED_COL_TABS  VIEW
USER_UPDATABLE_COLUMNS  VIEW
USER_USERS  VIEW
USER_USTATS   VIEW
USER_VARRAYS  VIEW
USER_VIEWS  VIEW
```

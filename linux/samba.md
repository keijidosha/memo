# Samba

## 設定
### 設定内容を確認する
* 設定された項目を確認する  
`testparm`
* 明示的に設定されていない項目についてもデフォルト値を表示する  
`testparm -v`

### Samba共有フォルダへのコピーを速度制限する
次の行を smb.conf に追記
```
max xmit = 2048
```

### OpenLDAP で認証を行う場合の設定
/etc/samba/smb.conf

```
dos charset = CP932
display charset = UTF-8
workgroup = WORKGROUP
netbios name = sambasvr
obey pam restrictions = Yes
passdb backend = ldapsam:ldap://10.1.2.3
ldap admin dn = uid=Administrator,ou=Users,dc=foo,dc=domain,dc=net
ldap group suffix = ou=Groups
ldap machine suffix = ou=Computers
ldap passwd sync = Yes
ldap suffix = dc=foo,dc=domain,dc=net
ldap user suffix = ou=Users
```

LDAP サーバに接続するためのパスワードを設定

```
smbpasswd -w <password>
Setting stored password for "uid=Administrator,ou=Users,dc=foo,dc=domain,dc=net" in secrets.tdb
```

### 削除ファイルをゴミ箱に移す設定
[global]セクションに  
`vfs objects = recycle`  
を記述。

各共有フォルダのセクションに

```
recycle:repository = .recycle/%u
recycle:keeptree = yes
recycle:touch = yes
recycle:versions = yes
recycle:maxsize = 100000000
recycle:exclude = *.tmp *.temp *.o *.obj ~$* *.~??
recycle:exclude_dir = /tmp|/cache
recycle:noversions = *.doc *.xls *.ppt
```

を記述。

* recycle:repository = .recycle/%u  
削除ファイルを移動するディレクトリ(共有パス直下の .recycle/ユーザID) (ファイルを削除すると、そのユーザしかゴミ箱にアクセスできないため、ユーザごとにゴミ箱を分ける。)
* recycle:keeptree = yes  
ディレクトリ構造を保ったまま .recycle に移動。
* recycle:touch = yes  
ゴミ箱に移された時に、ファイルのタイムスタンプを更新。  
後述の tmpwatch に対応するため。
* recycle:versions = yes  
同名のファイルがすでにゴミ箱に存在する場合、ファイル名に連番を付けて保存。
* recycle:maxsize = 100000000  
ゴミ箱に移動するファイルサイズの上限をバイト数で指定。
* recycle:exclude = *.tmp *.temp *.o *.obj ~$* *.~??  
ゴミ箱に移動せず、即座に削除するファイル名を指定。
* recycle:exclude_dir = /tmp|/cache  
指定されたディレクトリから削除された場合、ゴミ箱に移動せず、即座に削除。
* recycle:noversions = *.doc *.xls *.ppt  
すでに同名のファイルが削除されていても、連番を付けずに上書きするファイル名を指定。  
次に共有ディレクトリのパス直下に .recycle ディレクトリを作成してパーミッションを 777 に設定。  
(例) /opt/samba/share ディレクトリを共有している場合。

```
cd /opt/samba/share
mkdir .recycle
chmod 777 .recycle
```

さらに tmpwatch を cron で実行して、削除後一定期間経過したファイルをゴミ箱から削除。

`/usr/sbin/tmpwatch 168 /opt/samba/share/.recycle`  
168時間 ⇒ 1週間

## トラブルシューティング
### Windows 2000/XP から SAMBA の共有フォルダに接続できない
* 「ネットワークパスが見つかりません」というメッセージが表示される。
* コントロールパネル - ネットワーク - インターネットワークプロトコル(TCP/IP) - 詳細設定 - WINS タブで、  
「NetBIOS over TCP/IP を有効にする」  
に設定されているかどうかを確認。

## MacOS High Sierra から Samba 共有フォルダにファイルをコピーすると、ファイルのパーミッションに force create mode が反映されない。
グルーブとユーザが読めないファイルが作成される。
smb.conf の [global] セクションに、次の設定を追記  
`unix extensions = no`

### Samba 3.x に対して High Sierra から同じ名前のファイルを 2回コピーできない。
再現方法
1. High Sierra から Samba 3.x の共有フォルダにファイルをコピー
1. Samba 3.x が稼働するサーバ側で、コピーされたファイルを移動、または削除
1. High Sierra から Samba 3.x の共有フォルダに同じ名前のファイルをコピー  
「""という項目が存在するため、操作を完了できません。」というメッセージが表示される。

※回避方法  
smb.conf の [blobal] セクションに次の設定を追記  
`max protocol = SMB2`

Samba 3.x のデフォルト値は NT1  
Samba 4.x では SMB3(設定項目名は **server max protocol** に変更)

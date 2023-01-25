# SVN

* ブランチを作成
  ```
  svn copy svn+ssh://user@192.168.1.1/var/lib/svn/repository/projects-a/trunnk/abc
  svn+ssh://user@192.168.1.1/var/lib/svn/repository/projects-a//branches/1.10
  ```
* リビジョンを指定してブランチ(コピー)  
  ```
  svn copy -r 31 svn+ssh://user@192.168.1.1/var/lib/svn/repository/projects-a/trunnk/abc
  svn+ssh://user@192.168.1.1/var/lib/svn/repository/projects-a/branches/1.10
  ```
* リビジョン間の差分をソースにパッチとしてあてる  
  ```
  cd ~/svn/projects
  svn diff -r 501:502 svn+ssh://user@192.168.1.1/var/lib/svn/repository/projects-a/trunnk/abc | patch -p0
  ```
* タグを作成  
  ```
  svn copy svn+ssh://192.168.1.1/var/svn/projectshoge/trunk
  svn+ssh://192.168.1.1/var/svn/projectshoge/tags/revision_1.0 -m "tag message"
  ```
* SSHオプションを指定してチェックアウト  
  ```
  svn co --config-option "config:tunnels:ssh=ssh -p 12345 -i /home/user/.ssh/ssh_private_key -l user -q"
  svn+ssh://192.168.1.1/var/lib/svn/repository/projects-a/trunk/
  ```

## 移行

### コマンド


* カレントディレクトリ以下のファイルをサーバから更新  
  svn update  
  svn up
* カレントディレクトリ「以下」で編集中のファイルを一覧表示  
  svn st  
  svn status
* カレントディレクトリで編集中のファイルを一覧表示  
  svn st -N  
  svn status -N
  または svn status --non-recursive
* 他で更新されたファイルを表示。  
  sv st -u  
  svn status -u  
  または  
  svn status --show-updates
* 追加  
  * ディレクトリ  
    svn add <ディレクトリ名>  
    ディレクトリの add は再帰的に実行される。
  * ファイル  
    svn add <ファイル名>
* コミット  
  svn commit -m 'xxx' pom.xml  
  ディレクトリの commit は再帰的に実行される。
* 編集中のファイルを元に戻す  
  svn revert pom.xml
* 履歴一覧  
  svn log pom.xml
* サブディレクトリ以下にあるファイルの履歴詳細一覧  
  svn log -v <directoryname>
* サブディレクトリ以下にあるファイルのステータス一覧  
  svn status -v <directoryname>
* コマンドのヘルプ  
  svn help checkout
* チェックアウト  
  svn checkout svn+ssh://user@192.168.1.1/var/lib/svn/java/
* 特定のリビジョンをチェックアウト  
  svn checkout svn+ssh://user@192.168.1.1/var/lib/svn/java/@123
* エクスポート  
  svn export svn+ssh://user@192.168.1.1/var/lib/svn/java
* サーバ上のファイル一覧を参照  
  svn ls svn+ssh://user@192.168.1.1/var/lib/svn/java/
* ヘルプ  
  svn help

### サーバ上で実行するコマンド

* サーバ上でファイル一覧を表示  
  svn ls file:///home/svn/repo/trunk
* ディレクトリ作成  
  svn mkdir file:///home/svn/repo/foo
* プロパティ操作
  * プロパティ表示
    ```
    svn propget(pget,pg) svn:ignore file:///dir/program/source/trunk
    svn proplist(plist,pl) file:///dir/program/source/trunk
    ```
  * プロパティ設定
    ```
    svn propedit(pedit,pe) svn:ignore file:///dir/program/source/trunk
    svn propset(pset,ps) svn:ignore file:///dir/program/source/trunk
    ```
  * プロパティ削除  
    `svn propdel(pdel,pd) svn:ignore file:///dir/program/source/trunk`


## 属性

* 管理対象外ファイルを指定
  * svn:ignore
  * ディレクトリ名、ファイル名(複数指定する場合は改行区切り)
* ソースにリビジョン番号を埋め込む
  * svn:keywords
  * Id Revision
  * $Revision$

## 比較

* リビジョン間の比較  
  svn diff -r 567:568 http://foo.abc/svn/trunk/xxx/

## ブランチ

SSH接続の場合
```
svn copy svn+ssh://foo@192.168.1.1/var/lib/svn/repository/project/trunk \
         svn+ssh://foo@192.168.1.1/var/lib/svn/repository/project/branches/pilot_version \
-m "save pilot vertion."
```

## バックアップ

* Webtribe  
  svnadmin dump /var/lib/svn/java/ | gzip -c > svn_java.dump.gz

## tips

* ignore するディレクトリを間違ってコミットし、その後 ignore 属性を設定しても同期が取れなくなった場合  
  * まずサーバから更新(update)  
  * サーバ上のディレクトリを直接削除  
    svn delete -m '誤ってコミットしたファイルを削除' svn+ssh://user@host/var/lib/svn/repository/projects/<del dir>
  * 再度サーバから更新(udpate)


## 設定

xinetd 用ファイルを作成(svnserveで接続する場合)   
※svn+ssh で接続する場合は不要です。

/etc/xinetd.d/svn
```
service svn
{
       disable = no
       port            = 3690
       socket_type     = stream
       protocol        = tcp
       wait            = no
       user            = svn
       server          = /usr/bin/svnserve
       server_args     = -i -r /var/lib/svn/repository
#       only_from       = 192.168.10.0/24 192.168.1.0/24 127.0.0.1/32
}
```

### 初期設定

* mkdir /var/lib/svn
* useradd -d /var/lib/svn svn
* cd /var/lib/svn
* mkdir repository
* svnadmin create /var/lib/svn/repository
* cd repository/conf
* svnserve.conf, passwd, authz を編集
* passwdにユーザID とパスワードを登録
  ```
  [users]
  userfoo=password
  <code>
  ```
* authz にグループとディレクトリアクセス権限を登録
  ```
  [groups]
  admin=admin,manager
  
  [/]
  * = r
  @admin = rw
  
  [/java]
  userfoo = rw
  ```
* svnserve.conf を編集
  ```
  [general]
  anon-access = read
  auth-access = write
  
  password-db = passwd
  authz-db = authz
  ```

設定後はリロードなどしなくても即座に反映される。(以降のリクエストから)

## CVSからSubversionへの移行

http://cvs2svn.tigris.org/

./cvs2svn --encoding=euc_jp --fallback-encoding=cp932 --force-branch=RELEASE_0_9_6 --force-branch=RELEASE_0_9_5 -s ../repo/ ../root/

* --encoding  
  文字コード
* --fallback-encoding  
  文字コード変換でエラーが発生した時に使う文字コード
* --force-branch  
  指定されたリビジョンはブランチとして扱う*1
* --force-tag  
  指定されたリビジョンはタグとして扱う*2  

※ファイル名に日本語(特にOSと異なるエンコーディングで)が使われていると、エラーが発生する可能性がありますので、事前に削除しておきます。

## その他


* svn commit してもワーキングディレクトリ(ローカルマシンのディレクリト)のリビジョンが上がらない  
  SVNサーバ上のディレクトリはリビジョン番号が上がっているので、svn update してワーキングディレクトリに反映させる。


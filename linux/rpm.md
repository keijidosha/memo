# RPM

## コマンド

* パッケージがインストール済みか確認  
`rpm -q <パッケージ名>`
* パッケージ名の一部から検索する場合  
`rpm -qa | grep <パッケージ名の一部>`  
(例)  
`rpm -qa | grep get`
* あるコマンドがどのパッケージによってインストールされたかを確認  
`rpm -qf /usr/bin/ssh`  
* パッケージに含まれているファイルを表示  
`rpm -ql <パッケージ名>`  
RPM ファイルに含まれているファイルの一覧を表示する場合は  
`rpm -qlp <RPMファイル名>`
* パッケージ情報を表示  
`rpm -qi <パッケージ名>`  
`rpm -qip <RPMファイル名>`
* インストール時に実行されるスクリプトを表示  
`rpm -q --scripts <パッケージ名>`  
`rpm -qp --scripts <RPMファイル名>`
* ある RPM パッケージが依存するファイル、パッケージを表示  
`rpm -qR <パッケージ名>`  
`rpm -qRp <RPMファイル名>`
* ある RPM パッケージが依存するパッケージを表示  
  * 直接依存するパッケージを表示  
`rpm -q --requires <パッケージ名>`  
または  
`yum deplist <パッケージ名>`  
  * (例) bash が依存するパッケージを簡潔に表示  
`rpm -q --requires bash | cut -d ' ' -f 1 | xargs rpm -q --whatprovides | sort | uniq`  
  * ある RPM パッケージが依存するパッケージをファイルと対応付けて表示  
(例) bash が依存するパッケージを表示  
`rpm -q --requires bash | while read line; do echo -n "$line:  "; echo $line | cut -d ' ' -f 1 | xargs rpm -q --whatprovides; done`  
依存するファイルを提供するパッケージが 2個以上インストールされている場合、改行して(複数行に渡って)表示される。  
* RPM が提供するファイルに依存しているパッケージを表示  
`rpm -q --provides xxx | cut -d ' ' -f 1 | xargs rpm -q --whatrequires | sort | uniq`
* 依存性を無視してインストール  
`rpm -ivh --nodeps <RPMファイル名>`  
* 強制上書きインストール(のテスト)  
`rpm -Uvh --force --test <RPMファイル名>`
* パッケージの検索  
`rpm -V <パッケージ名>`
* RPM に含まれるファイルを展開する  
`rpm2cpio <RPMファイル名> | cpio -id`
* RPM に含まれるファイルの一覧を表示する  
`rpm2cpio <RPMファイル名> | cpio -t`
* RPM に含まれるファイル名を指定して取り出す  
`rpm2cpio <RPMファイル名> | cpio -id ./tmp/hoge.txt`
* RPM に含まれるディレクトリ名を指定して取り出す  
`rpm2cpio <RPMファイル名> | cpio -id ./tmp/*`
* GPG KEY の一覧を表示  
`rpm -q gpg-pubkey --qf '%{name}-%{version}-%{release} --> %{summary}\n'`  
(出典) http://syaka-syaka.blogspot.com/2016/01/rpmgpg.html


## パッケージ作成

### SRPM から RPM を作成

※rpm-build パッケージが必要です。

### ディレクトリ作成
```
mkdir -p rpm/{BUILD,SOURCES,SPECS,SRPMS,RPMS/{i386,i586,i686,noarch}}
echo "%_topdir /home/okumura/rpm" > ~/.rpmmacros
rpmbuild --rebuild xxx.srpm
```

### SRPM を展開し、ソースファイルを入れ替えて PRM を作成

* ディレクトリ作成  
  ```
  mkdir -p rpm/{BUILD,SOURCES,SPECS,SRPMS,RPMS/{i386,i586,i686,noarch}}
  echo "%_topdir /home/okumura/rpm" > ~/.rpmmacros
  ```
* SRPM ファイル展開  
`rpm -i postgresql-8.1.9-1.el5.src.rpm`
* ソース入れ替え  
上記の操作で SRPM に含まれるソースファイルの tar.gz や patch が展開されるので、ソースファイルの tar.gz を入れ替えます。
* SPECファイル編集  
SPEC ファイルに書かれているソースの tar.gz ファイル名を、入れ替えた tar.gz の名前に変えておきます。  
バージョン記述も合わせて書き換えておきましょう。  
* パッケージを展開(%build の手前まで、build prep， %prepを実行)  
`rpmbuild -bp SPECS/postgresql.spec`
* make までを実行  
`rpmbuild -bc SPECS/postgresql.spec`
* install までを実行  
`rpmbuild -bi SPECS/postgresql.spec`
* srpm だけを作成  
`rpmbuild -bs SPECS/postgresql.spec`
* rpm だけを作成  
`rpmbuild -bb SPECS/postgresql.spec`
* rpm と srpm を作成  
`rpmbuild -ba SPECS/postgresql.spec`

# yum


* RPM が依存するパッケージをダウンロード  
`yum install --downloadonly --downloaddir=/tmp/hoge/ hoge`
  * x86_64 と i686 間でのバージョン衝突を避けるため、updates リポジトリを参照しないようにする  
    `yum install --downloadonly --downloaddir=/tmp/hoge/ --disablerepo=updates hoge`
* ダウンロードしたパッケージをアップデートする  
`yum --nogpgcheck --disablerepo=* update *.rpm`
* ダウンロードしたパッケージをオフラインでインストールする  
`yum --nogpgcheck --disablerepo=* localinstall *.rpm`

#### yumキャッシュのクリア
```
yum clean all
rm -rf /var/cache/yum/
```
RHEL 8.x 系は
```
yum clean all
rm -rf /var/cache/dnf/
```

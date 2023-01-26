# brew

* インストール済みボトルの一覧  
`brew list`  
または `brew list xxx`  
(例) `brew list httpd`
* ボトルの検索  
`brew search xxx`  
(例) `brew search httpd`
* ボトルの詳細を表示  
`brew info xxx`
* ボトルをインストール  
`brew install xxx`
* ボトルの更新  
`brew upgrade xxx`

## トラブルシューティング

### brew upgrade すると、MySQL が起動できなくなる

問題) brew upgrade すると、brew でインストールした MySQL が次のメッセージを出力して起動できなくなる。

> Can't read dir of '/usr/local/etc/my.cnf.d'

対応) ディレクトリを作成してあげる

`mkdir /usr/local/etc/my.cnf.d`

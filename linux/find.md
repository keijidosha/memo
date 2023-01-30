# find

## 更新日付で検索
* 60分以内に変更されたファイルを検索  
`find /var/log -mmin -60`
* 1日以上前に変更されたファイルを検索  
`find /var/log -mtime +1`

### a/c/m, time/min の使い分け
* atime, amin  
アクセス日時を対象として検索
* ctime, cmin  
inode(オーナー、パーミッションなど)への更新日時を対象として検索
* mtime, mmin  
ファイルの内容への更新時刻を対象として検索
* xtime: 日数を指定  
xmin: 分を指定

# RHEL


## サブスクリプション管理
* サブスクリプションの一覧を表示  
`subscription-manager list`
* サブスクリプション管理にログイン  
`subscription-manager register --username='hoge' --password='fuga'`
* サブスクリプション管理からログアウト  
`subscription-manager unregister`
* 利用可能なサブスクリプションの一覧を表示  
`subscription-manager list --available`  
サブスクリプションの一覧は次の URL からでも可能。  
https://access.redhat.com/management/subscriptions
* 利用可能なサブスクリプションのプール ID だけを表示  
`subscription-manager list --available --pool-only`
* サブスクリプションを登録  
`subscription-manager attach --pool <poolid>`
* サブスクリプションを解除  
`subscription-manager remove --all`

(参考)  
* [Red Hat Developer Subscriptionを取得して、RHELをサブスクリプション登録・登録解除する方法](https://tech-mmmm.blogspot.com/2021/02/red-hat-developer-subscriptionrhel.html)  
* [RHELの開発者用サブスクリプションを取得する方法](https://qiita.com/SkyLaptor/items/31eb7b506339718455d4)

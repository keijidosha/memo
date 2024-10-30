# RHEL

- Table of Content  
{:toc}

## サブスクリプション管理
* サブスクリプションの一覧を表示  
  `subscription-manager list`
* サブスクリプション管理にログイン  
  `subscription-manager register --username='hoge' --password='fuga'`
* サブスクリプション管理からログアウト  
  `subscription-manager unregister`
* サブスクリプトション管理からログアウト(unregister)せずに OS を削除してしまった場合
  1. [Red Hat Subscription Management](https://access.redhat.com/management) にログイン。
  1. [System Inventory] をクリック。
  1. 該当する Name の右端にある：(縦の3点)をクリックして[Delete] をクリック。  
     Name には Linux のホスト名が表示される。

2024.10.01 現在、SCAにより、サブスクリプションをアタッチしなくても register 時に自動的にサブスクリプション(yumリポジトリ)へのアクセスが可能になるため、以下の attach/remove は不要になり、register/unregister だけでよくなった模様。
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

### サブスクリプションが切れてしまった場合

subscription-manager register を実行すると、新しいサブスクリプションが作成される。  
(参考) [How to activate your no-cost Red Hat Enterprise Linux subscription](https://developers.redhat.com/blog/2021/02/10/how-to-activate-your-no-cost-red-hat-enterprise-linux-subscription#step_2__download_no_cost_rhel)  
(例)  
```
# Dokcer で RHEL のコンテナを起動
$ docker run -it --rm -v /vagrant:/vagrant --name rh8 registry.access.redhat.com/ubi8/ubi:8.4 /bin/bash
# サブスクリプションを登録 => このコンテナでサブスクリプションを 1つ消費することになるので、後で解除。
$ subscription-manager register --username=hoge --password=pass
Registering to: subscription.rhsm.redhat.com:443/subscription
The system has been registered with ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
The registered system name is: xxxxxxxxxxxx
# このシステム(コンテナ)からサブスクリプションの使用を解除。
$ subscription-manager unregister
```

(参考)  
* [Red Hat Developer Subscriptionを取得して、RHELをサブスクリプション登録・登録解除する方法](https://tech-mmmm.blogspot.com/2021/02/red-hat-developer-subscriptionrhel.html)  
* [RHELの開発者用サブスクリプションを取得する方法](https://qiita.com/SkyLaptor/items/31eb7b506339718455d4)

## EPEL

* EPEL を有効にする  
  ```
  subscription-manager repos --enable codeready-builder-for-rhel-8-$(arch)-rpms
  dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
  ```  
  (参考)  
  [あらためてEPELリポジトリの使い方をまとめてみた](https://qiita.com/yamada-hakase/items/fdf9c276b9cae51b3633)  
  [Getting Started with EPEL](https://docs.fedoraproject.org/en-US/epel/getting-started/)  
  

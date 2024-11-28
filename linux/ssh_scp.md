# SSH, SCP

## Tips
### キーペア作成
```
ssh-keygen -t ed25519 -P "" -f hoge.pem
```
(参考) [2017年版 SSH公開鍵認証で使用する秘密鍵ペアの作り方](https://qiita.com/wnoguchi/items/a72a042bb8159c35d056#%E5%9C%A7%E5%80%92%E7%9A%84%E3%81%AB%E6%84%8F%E8%AD%98%E9%AB%98%E3%81%84-ed25519)

### 一定時間経過すると自動的にログアウトする。
環境変数 TMOUT を設定。  
(例) /etc/profile に
```
export TMOUT=300
```
を指定すると、5分間操作がなかった時に自動ログアウトされます。

### ネットワークの問題(NATテーブルのタイムアウトなど)により、一定時間操作しないと切断されてしまう。
* サーバ側で設定する  
/etc/ssh/sshd_config に指定する。(60秒間隔で keepalive パケットを送信する。)  
  ```
  ClientAliveInterval 60
  ```
* クライアント側で設定する  
~/.ssh/config に指定する  
  ```
  Host hoge.example.com
     ServerAliveInterval 60
     ServerAliveCountMax 3
  ```
* コマンドラインで指定する   
  ```
  ssh -o ServerAliveInterval=60 host
  ```

### IPv4 だけで接続する、または Ipv6 だけで接続する。

~/.ssh/config に指定する  
* IPv4 だけで接続する
  ```
  Host hoge.example.com
     AddressFamily inet
  ```
* IPv6 だけで接続する
  ```
  Host hoge.example.com
     AddressFamily inet6
  ```

### ホストに初めて接続すると時、ホストキーをチェックせず、known_hosts ファイルにも記録しない
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null 192.168.1.1
* 設定ファイルに記述する  
  ```
  vi ~/.ssh/config
  Host 192.168.1.*
     StrictHostKeyChecking no
     UserKnownHostsFile=/dev/null
  ```
* 複数ホスト指定する場合はスペースで区切る  
  ```
  Host www.hoge.com www.fuga.com
     StrictHostKeyChecking no
     UserKnownHostsFile=/dev/null
  ```

### 参照する秘密鍵の優先順位

1. ~/.ssh/config に接続先ホストで使用する秘密鍵が指定されているとそれを使う。
1. ~/.ssh/ に次の名前のファイルがあるとそれを使う。
   ```
   id_rsa
   id_dsa
   id_ecdsa
   id_ed25519
   ```
1. ssh-agent を使って秘密鍵をメモリー上に登録しているとそれを使う。

### 秘密鍵のパスフレーズを変更

```
ssh-keygen -p -f ~/.ssh/<private_key_file>
```  
パスフレーズを削除する場合は、新しいパスフレーズを入力するプロンプトで空 Enter

## ポートフォワード
### ローカルポートフォワードでリスンするポートを別ホストからも接続できるよう、全NICにバインドしたい
`ssh -g -L 22:anotherhost:54322 user@host`  
-g パラメータを付ける  
* -g なし  
`0/0/128        127.0.0.1.54322`
* -g あり  
`0/0/128        *.54322`

### リモートフォワードで、リモートホストがリスンするポートを、別ホストからも接続できるよう、全NICにバインドしたい
リモートホストの /etc/ssh/sshd_config に次の行を追記  
`GatewayPorts yes`

## ファイル転送
### scpでコピーしたいが、1回の通信でコピー後にリネームまで行いたい
ssh経由で標準入力からファイルに書き込み、リネームするコマンドを発行する
```
cat hoge.bin | ssh usr@192.168.1.1 'cat - > /tmp/hoge.tmp; mv /tmp/hoge.{tmp,bin}'
```
pv コマンドを挟むと、転送速度の調節(帯域制限)できる
```
cat hoge.bin | pv -L 1m | ssh usr@192.168.1.1 'cat - > /tmp/hoge.tmp; mv /tmp/hoge.{tmp,bin}'
```

### scp でスペースを含むディレクトリに対してコピーする
(例) vmware 仮想イメージをディレクトリごとコピーする場合
```
scp -pr centos root@172.16.1.1":/var/lib/vmware/Virtual\ Machines/
```

## 設定ファイル

設定ファイルのパスを指定する。  
`-F <config path>`  
SSH でポートフォワードする時、1023 より下のポート番号を転送元に指定する場合、管理者権限が必要になるものの、SSH 接続設定ファイルは一般ユーザー配下に作成していて管理者権限では参照されない場合など。
```
ssh hoge -L 80:127.0.0.1:80 -F ~hoge/.ssh/conf.d/hoge.conf
```

## 認証
### ssh-agent を使って、秘密鍵を追加
```
eval `ssh-agent`
ssh-add <秘密鍵のパス>
```

## トラブルシューティング

### Too many authentication failures でログインできない
1.  コマンドラインパラメーターに「-o PreferredAuthentications=password」を追加する。  
(例)  
ssh -o PreferredAuthentications=password 192.168.1.1  
1. ssh-add -D を実行してみる。 

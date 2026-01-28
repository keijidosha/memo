# SourceTree

## 設定
* 移動・リネームされたファイルに対するログ表示で、「追跡」を常に有効にする。  
  => git log で --follow パラメーターを指定するのと同じ動きにする。  
  「config ファイルを編集」をクリックし、次の行を追加。
  ```
  [log]
      follow = 1
  ```
* ファイルの内容が変わっていないのにコミット対象になる  
ファイルのパーミッションが変わっていると、差分がなくてもコミット対象になる。
* SSH接続がうまくいかない場合は  
~/.ssh/config で「Host bitbucket.org」の設定を確認。  
指定されている秘密鍵に対応する公開鍵が BitBucket に登録されているかどうかなど。

## トラブルシューティング

* sourcetree でコミット履歴をさかのぼると、次のメッセージが表示されて動かなくなる。
  ```
  Loding 100 commits to reach commit
  ```
  [ツール] - [オプション] - [全般] - [1度に読み込むログの行数] を 0 に設定すると回避できた。  
  (参考) [Sourcetree gets stuck loading some commits](https://jira.atlassian.com/browse/SRCTREEWIN-14551)

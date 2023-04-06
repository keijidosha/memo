# git

Table of Contents
-----------------

* [git](#git)
   * [使い方](#使い方)
   * [サーバでGitリポジトリ作成](#サーバでgitリポジトリ作成)
      * [サーバ側](#サーバ側)
      * [クライアント側](#クライアント側)
         * [ブランチ](#ブランチ)
   * [環境設定](#環境設定)
      * [Mac に git リポジトリを作成し、SourceTree から接続する](#mac-に-git-リポジトリを作成しsourcetree-から接続する)
      * [Mac で秘密鍵を使って SSH で github に接続する](#mac-で秘密鍵を使って-ssh-で-github-に接続する)
      * [削除](#削除)
   * [トラブルシューティング](#トラブルシューティング)
      * [ブランチ](#ブランチ-1)
      * [タグ](#タグ)
      * [コミット](#コミット)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## 使い方
* コミット間での差分比較  
`git diff <比較元ハッシュ値>..<比較先ハッシュ値> [比較ファイル]`

## サーバでGitリポジトリ作成
### サーバ側
* リポジトリ用ディレクトリを作成  
`mkdir -p ~/git/repos/hogeproject`  
`cd ~/git/repos/hogeproject`
* リポジトリ用に初期化  
`git --bare init --shared`
* Mac の共有設定で、リモートログインを有効にする  

### クライアント側
* SourceTree の "新規リポジトリ" で "URLからクローン" を選択する。  
SourceTree から接続する時に指定するURL  
`ssh://user@127.0.0.1/Users/hoge/git/repos/hogeproject`

あとは接続時に指定したローカルディレクトリにファイルを置いていって、コミット、プッシュする

#### ブランチ

* ブランチの一覧を表示  
  * ローカルブランチの一覧  
    `git branch`
  * リモートも含めたブランチの一覧  
    `git branch -a`
* ブランチを切り替え  
`git checkout <ブランチ名>`
* ブランチ名を変更  
`git branch -m <旧ブランチ名> <新ブランチ名>`


## 環境設定
### Mac に git リポジトリを作成し、SourceTree から接続する

```
# リポジトリ用ディレクトリを作成
mkdir -p ~/git/repos/hogeproject
cd ~/git/repos/hogeproject
# リポジトリ用に初期化
git --bare init --shared
# Mac の共有設定で、リモートログインを有効にする
SourceTree の "新規リポジトリ" で "URLからクローン" を選択する。
# SourceTree から接続する時に指定するURL
ssh://user@127.0.0.1/Users/hoge/git/repos/hogeproject
あとは接続時に指定したローカルディレクトリにファイルを置いていって、コミット、プッシュする
```
Mac で SSH 接続を許可するため、[システム環境設定] - [共有] - [リモートログイン] を許可しておく。

### Mac で秘密鍵を使って SSH で github に接続する

1. ~/.ssh/config に次の設定を追記
   ```
   AddKeysToAgent yes
   UseKeychain yes
   
   Host github.com
       user <githubアカウント>
       IdentityFile <秘密鍵のパス>
   ```
1. SSH秘密鍵のパスフレーズをキーチェーンに登録  
   `ssh-add --apple-use-keychain <秘密鍵のパス>`  
   => パスフレーズをきかれるので入力

### 削除
* ファイル削除  
  ```
  git rm hoge.txt
  ```
* ローカルにファイルを残したまま管理対象から除外  
  ```
  git rm --cached hoge.txt
  ```
* ディレクトリ削除  
  ```
  git rm -r examples
  ```

## トラブルシューティング
### ブランチ
* ブランチをし忘れてソースを編集してしまった  
コミット前であれば、-b で編集中のソースを残したまま、ブランチを作成してチェックアウトできる  
  ```
  git checkout -b <新規ブランチ名>
  ```
  (参考) [ブランチを作り忘れた時](https://qiita.com/k6i/items/edc69a806095e4fc489c)
* リモートブランチが表示されない(チェックアウトできない)  
`git branch -a` しても、リモートにあるブランチが表示されない。  
`git remote show origin` を実行して、ブランチの状態を表示。  
次のように表示される。  
  ```
  <ブランチ名>    new (next fetch will store in remotes/origin)
  ```
  そこで `git fetch --all` を実行して、最新の情報を取得する。

### タグ
* pushするとエラーになり、「hint: Updates were rejected because the tag already exists in the remote.」というメッセージが出力される。    
ターミナルから次のコマンドを実行。  
  ```
  git pull --tags
  ```

### コミット
* プッシュする前のコミットをなかったことによる  
=> ブランチ内での HEAD を指定したコミットに移動する  
  ```
  git reset --hard <コミットID>
  ```  
(参考) [プッシュ前のローカルのコミットを取り消す方法](https://qiita.com/toohsk/items/d32a5820ca1a5eefc231)

### SSH接続
* bitbucket に ssh で接続するようにすると、プッシュ/プルに数分かかってしまう。  
  DNS に bitbucket.org を問い合わせると、IPv4 と IPv6 の IP が返ってくる。  
  すると git から呼び出された ssh はまず IPv6 に SSH 接続しようとするが、応答が返って来ずタイムアウトするまで待機。  
  その後で ssh は IPv4 で接続し、プッシュ/プルができるようになる。  
  そこで ~/.ssh/config に「AddressFamily inet」を記述し、IPv6 接続しないようにする。  
  ```
  Host bitbucket.org
    HostName bitbucket.org
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/privatekey
    UseKeychain yes
    AddKeysToAgent yes
    AddressFamily inet
  ```
  ssh が bitbucket に IPv6 で接続する様子は次のコマンドで確認。  
  `netstat -an | grep "\.22 "`

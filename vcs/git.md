- Table of Content  
{:toc}

# git

## 使い方
* クローンして特定のブランチをチェックアウト(SSH 接続の例)
  ```
  # クローン
  git clone git@github.com:hoge/fuga.git
  cd fuga/
  # チェックアウト
  git checkout develop
  # カレントのブランチを確認
  git branch
  ```  
  クローンからチェックアウトまでをコマンド 1つで行う場合。  
  ```
  git clone git@bitbucket.org:nextgen-rd/hoge.git -b develop
  ```
* ブランチ作成からコミット/プッシュ/タグ付けまでの流れ
  * ブランチ作成  
    `git branch <ブランチ名>`
  * ブランチの切り替え  
    `git checkout <ブランチ名>`
  * ブランチを作成して切り替え  
    `git checkout -b <ブランチ名>`
  * 現在のブランチの確認  
    `git branch`  
    リモートブランチも表示する場合は -a を指定  
    `git -a branch`
  * ファイルの編集状態を確認  
    `git status`  
  * 新規ファイル追加  
    `git add .`
  * コミット  
    `git commit -m <message>`
    * コミットメッセージを複数行指定  
      ヒアドキュメントで指定
      ```
      git commit -F- << EOM
      > hoge
      > fuga
      > EOM
      ```
      vim などのエディタで入力
      ```
      git commit
      ```
      エディタが起動するのでメッセージを入力
  * プッシュ  
    `git push origin HEAD`
  * タグ付け  
    `git tag <tag>`  
    コメント付きのタグをつける場合
    `git tag <tag> -m 'comment'`
  * タグを PUSH  
    `git push origin <tag>`
* 修正中(未コミット)ソースの差分表示  
  `git diff -w`  
  -w: 空白を無視する
* コミット間での差分比較  
  `git diff <比較元ハッシュ値>..<比較先ハッシュ値> [比較ファイル]`  
  タグ間での差分比較も同じ  
  `git diff <変更元タグ>..<変更先タグ>`
* 差分表示  
  `git diff --cached -w`
* blame(アノテーション)表示  
  `git blame <ファイル名>`
* 変更量表示  
  `git diff --stat <変更前タグ> <変更後タグ>`
* プッシュ 〜 マージまで
  ```
  git push -u orign HEAD
  git checkout main
  git merge -
  git push origin HEAD
  ```

## コミット情報取得

Go ビルド時にバージョン情報を埋め込む場合などに使用。

* コミットのハッシュ値を取得。
  * 全部
    ```
    git log --pretty=format:%H -n 1
    ```
  * 短い形式
    ```
    git rev-parse --short HEAD
    ```
* 直近のラベルを取得
  ```
  git describe --tags --abbrev=0
  ```

Go ビルド時に埋め込む

* ソース
  ```go
  package main

  var version string
  var revision string

  func main() {
      fmt.Printf("%s, %s\n", version, revision)
  }
  ```
* ビルド
  ```bash
  go build -ldflags "-X main.version=$(git describe --tags --abbrev=0) -X main.revision=$(git rev-parse --short HEAD)"
  ```

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
合わせて「リモートユーザーのフルディスクアクセスを許可」をチェックしておく。

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
  git reset --soft HEAD^
  ```  
  または  
  ```
  git reset --hard <コミットID>
  ```  
(参考)  
[[Git]コミットの取り消し、打ち消し、上書き](https://qiita.com/shuntaro_tamura/items/06281261d893acf049ed)  
[プッシュ前のローカルのコミットを取り消す方法](https://qiita.com/toohsk/items/d32a5820ca1a5eefc231)

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

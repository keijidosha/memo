# Mac

Table of Contents
-----------------

* [Mac](#mac)
   * [Spotlight](#spotlight)
   * [Tips](#tips)
   * [pmset](#pmset)
   * [トラブルシューティング](#トラブルシューティング)
   * [起動](#起動)
   * [PATH設定](#path設定)
   * [ファイル操作](#ファイル操作)
   * [ユーザー、グループ操作](#ユーザーグループ操作)
   * [Finder で隠しファイルの表示・非表示を切り替え](#finder-で隠しファイルの表示非表示を切り替え)
   * [その他](#その他)
      * [com.apple.quarantine](#comapplequarantine)
   * [ネットワーク](#ネットワーク)
      * [TCP/UDP/IP](#tcpudpip)
      * [Samba](#samba)
      * [SSH](#ssh)
   * [Timemachine](#timemachine)
   * [plist](#plist)
      * [形式変換](#形式変換)
   * [iTunes](#itunes)
   * [コマンド](#コマンド)
   * [その他](#その他-1)
      * [ランダムパスワード生成](#ランダムパスワード生成)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## Spotlight
* 拡張子での絞り込みは「ファイル名:*.txt」
* FreeMind ファイル検索時は「種類:ディベロッパ」
* そのファイルの場所を開くには、「command + R」キーを押します
* ターミナルからの検索例  
mdfind -onlyin ~ "kMDItemDisplayName='\*.txt'"  
mdfind -onlyin ~ "kMDItemTextContent=='\*検索文字列\*'"  
mdfind -onlyin ~ "kMDItemDisplayName='\*.txt' && "kMDItemTextContent=='\*検索文字列\*'"  

## Tips

* FTPサーバを起動・停止する
  * 起動  
  ```sudo launchctl load -w /System/Library/LaunchDaemons/ftp.plist```
  * 停止  
  ```sudo launchctl unload -w /System/Library/LaunchDaemons/ftp.plist```
* アプリを 2個起動する
  * アプリを起動する  
    `open -n <アプリのパス, 例:/Application/Hoge.app>`
  * ファイルを開く  
    `open -n <アプリと関連づけられたファイル>`
* スクリーンショットの保存先フォルダを指定(変更)する  
`defaults write com.apple.screencapture location <保存先フォルダ>/;killall SystemUIServer`  
(参考) [Macのスクリーンショットの保存先を変更する](https://book.mynavi.jp/macfan/detail_summary/id=90165)  
デフォルトに戻す場合は  
`defaults delete com.apple.screencapture location; killall SystemUIServer`  
(参考) [Mac - スクリーンショットで撮影した画像の保存場所を変更](https://pc-karuma.net/mac-customize-screencapture-location/)
* tar で圧縮する時、._ で始まる隠しファイルが含まれないようにする。  
COPYFILE_DISABLE=1 を付けて実行する。  
(例) `COPYFILE_DISABLE=1 tar zcvf hoge.tar.gz hoge/`
* アカウント切り替え時に、Tunnelblick が起動しないようにする(二重起動を防ぐ)  
Tunnelblick を起動させたくないアカウント配下の、次のファイルを別の場所に移動、または削除。  
`~/Library/LaunchAgents/net.tunnelblick.tunnelblick.LaunchAtLogin.plist`
* ISO イメージをマウント  
`hdiutil attach xxx.iso`  
アンマウント  
`hdiutil detach /Volumes/xxx`  
* sudo で入力するパスワードのタイムアウト時間を変更  
(例) 1時間に設定する場合  
sudo visudo  
`Defaults    timestamp_timeout=3600`


## pmset

|項目名|説明|
|:---|---|
|sleep|スリープさせるまでの時間|
|displaysleep|ディスプレイをスリープさせるまでの時間|
|womp|ネットワークからのスリープ解除を - 1:有効, 0:無効|
|ttyskeepawake|リモートログイン中は - 1:スリープしない, 0:する|
|hibernatemode|0:sleep, 3:safe sleep, 25:deep sleep|

* pmset sleepnow  
今すぐスリープする

## トラブルシューティング

* Touch Bar のある Mac で、日本語変換をキャンセルするのに、ESCキーを 2度押ししないと戻れない。  
[システム環境設定] - [キーボード] - [ユーザー辞書] で [Touch Barに入力候補を表示] のチェックを外すと解消。
* df コマンドで表示されるディスク残量が、Finder のステータスバーに表示される空き容量と比較してかなり少ない  
ローカルスナップショットで使っている容量が Finder には解放対象として使用量に含まれていないため。  
  * ローカルスナップショット一覧表示  
    tmutil listlocalsnapshots /  
    ```
    com.apple.TimeMachine.2021-02-23-142244
    com.apple.TimeMachine.2021-02-24-075912
    ```
  * ローカルスナップショットを削除  
`sudo tmutil deletelocalsnapshots 2021-02-23-142244`


## 起動

* セーフモード  
`Shift`
* 可能なメディアを選択して起動  
`C`
* Apple Hardware Test または Apple Diagnostics で起動  
`D`
* OS X 復元システムから起動  
`Command + R`
* NVRAM をリセット  
`Command + Option + P + R`
* Verboseモード  
`Command + V`
* Verboseモードでセーフブート  
`Command + Shift + V`
* リカバリーモード  
`Command + R`
* 外部ディスクから起動  
`Option`
* シングルユーザモード  
`Command + S`


## PATH設定

* ツールコマンドなどのPATHを各ユーザ共通で設定  
/private/etc/paths.d/ 以下にパスを記述したファイルを配置  
(例) /private/etc/paths.d/java  
/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/bin  

## ファイル操作

* ファイルの内容を16進表示  
hexdump -C hoge.txt  
od -tx1 hoge.txt  
* ファイルをバイナリ比較  
cmp hoge1.bin hoge2.bin
* ZIPファイルにパスワードをつける  
zipcloak <ZIPファイル名>

## ユーザー、グループ操作

* ユーザー一覧表示  
`dscl . list /Users`
* グループ一覧表示  
`dscl . list /Groups`
* グループ所属メンバー表示  
`dscl . read /Groups/<groupname>`
* グループにメンバーを追加  
`dscl . append /Groups/<groupname> GroupMembership <username>`
* グループからメンバーを削除  
`dscl . delete /Groups/<groupname> GroupMembership <username>`


(例) Wireshark で「There are no interfaces」と表示される場合
```
dscl . list /Groups
...
access_bpf
...

dscl . read /Groups/access_bpf
AppleMetaNodeLocation: /Local/Default
GeneratedUID: 3724111F-BDED-49C1-ADFD-ABA91D27B7C5
GroupMembers: EED29681-4027-4923-A32B-635CB2B29751 23B57DCE-500C-4632-8F4E-E9E65CBE52D8
GroupMembership: user1 user2
PrimaryGroupID: 501
RecordName: access_bpf
RecordType: dsRecTypeStandard:Groups

sudo dscl . append /Groups/access_bpf GroupMembership username
```


## Finder で隠しファイルの表示・非表示を切り替え

* Command + Shift + .(ピリオド)

Sierra では OK  
Yosemite 以前では「開くダイアログ」の中だけで使えた可能性あり  
Lepard 以前では使えない可能性あり  

## その他

### com.apple.quarantine

* ダウンロードされたファイルであることを示す属性と思われる。  
  ls -l で表示すると、permission に @ が表示される。
  ```
  ls -l
  drwxr-xr-x@ 33 root  wheel   1122  7 29 18:13 Sources/
  ```
* ls -l@ で詳細の確認
  ```
  ls -l@
  drwxr-xr-x@ 33 root  wheel   1122  7 29 18:13 Sources/
   com.apple.quarantine    71 
  ```
* xattr で詳細を確認
  ```
  xattr -l Sources
  com.apple.quarantine: 0001;51da1da2;Google\x20Chrome.app;35B71991-0D9C-4CA7-AE38-560824D0B830
  ```
* xattr で属性を削除  
`xattr -d com.apple.quarantine Sources`

## ネットワーク

### TCP/UDP/IP

* LISTENしているTCPポート番号の一覧を表示  
`sudo lsof -nP -i4TCP -sTCP:LISTEN`  
または  
`netstat -aLn`
* 確立したTCP接続の一覧を表示  
`sudo lsof -nP -i4TCP -sTCP:ESTABLISHED`  
または、接続中、クローズ中の一覧を表示  
`netstat -An -p tcp`
* ネットワークインターフェースごとの送受信パケット、バイト数を表示  
`netstat -ib`

### Samba

* Windows/Samba共有フォルダーをターミナルからマウントする  
  `mount -t smbfs //user:password@server/shared_folder mount_point`  
  (例)  
  `mount -t smbfs -r //hoge:hogepass@192.168.1.1/doc ~/doc`  
* サーバーのバージョンが古く、「Permission denied」エラーになる場合は、~/Library/Preferences/nsmb.conf を作成し、次の内容を設定してみる。  
  ```
  [default]
  signing_required=no
  ```
* 共有名に日本語が含まれていてターミナルからマウントできない場合  
`mount -t smbfs //user:password@server/`echo '共有名' | nkf -WwMQ | tr = %` mount_point`
* Windows/Sambaサーバが共有(公開)しているフォルダーの一覧を表示する  
`smbutil view //[domain;][userid[:password]@:host_or_ip`  
(例)  
`smbutil view //user:pass@192.168.1.1`

### SSH

* 秘密鍵のパスフレーズがキーチェーンに保存されない  
次のコマンドを実行して、パスフレーズを入力する。  
`ssh-add --apple-use-keychain <秘密鍵のパス>`

## Timemachine

* USB接続などした外付けドライブのルートにある tmp ディレクトリはバックアップされない。  
/System/Library/CoreServices/backupd.bundle/Contents/Resources/StdExclusions.plist に次のように記述されているためと思われます。  
`<string>/tmp</string>`  
つまり内蔵ディスクの /tmp をバックアップ対象からはずそうとして、すべてのドライブの /tmp がバックアップされないのではないかと。  
* ローカルのスナップショット領域を削除して解放する  
  外付けHDDが接続されていない(バックアップできない)間にローカルディスクに作成されたスナップショットを削除して解放  
  * スナップショットで使っている容量を確認  
    `sudo du -sh /.MobileBackups`
  * スナップショット機能を無効にして領域を解放  
    `sudo tmutil disablelocal`
  * スナップショット機能を有効にする  
    `sudo tmutil enablelocal`

## plist

### 形式変換

* バイナリ形式から XML 形式に変換(元ファイルを直接変換)  
`plutil -convert xml1 xxx.plist`
* 変換結果を別ファイルに出力する場合は -o filename を追加指定  
`plutil -convert xml1 xxx.plist -o hoge.xml`

* 指定可能な形式  
  xml, json, binary1

## iTunes

* iTunes のライブラリフォルダを指定し直す  
optionキー(Windowsの場合はシフトキー)を押しながら iTunes を起動すると、ライブラリフォルダを選択するダイアログが表示されます。

## コマンド

* ls コマンドで最終更新日時を秒まで表示する  
`ls -lT [file>`

## その他

### ランダムパスワード生成

`L=12; cat /dev/urandom | LC_CTYPE=C tr -dc '[:alnum:]' | fold -w $L | head -c $L; echo ""`  
L に生成するパスワードの文字列長を指定


# Mac: El Capitan

## git svn と Rootless

git svnを実行すると「Can't locate SVN/Core.pm in @INC」というエラーが発生  
(今回は Source Tree でプッシュを実行した時に発生)  
そこで http://anton0825.hatenablog.com/entry/20140221/1392974288 に書かれている次のコマンドを実行すると、Operation not permitted が発生。  
sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN/ /System/Library/Perl/Extras/5.18/auto/SVN  
==> Rootless が原因らしい(http://qiita.com/tuckQ/items/26c0bebbdfa2e094dba8)  
そこで次のコマンドを実行して、Xcode の Perl を @INC パスに追加。  

* @INC のパスを確認  
  perl -E 'say for @INC'
  ```
  /Library/Perl/5.18/darwin-thread-multi-2level
  /Library/Perl/5.18
  /Network/Library/Perl/5.18/darwin-thread-multi-2level
  /Network/Library/Perl/5.18
  /Library/Perl/Updates/5.18.2
  /System/Library/Perl/5.18/darwin-thread-multi-2level
  /System/Library/Perl/5.18
  /System/Library/Perl/Extras/5.18/darwin-thread-multi-2level
  /System/Library/Perl/Extras/5.18
  . 
  ```
* @INC で実際に存在するパスを確認  
perl -E '-d and say for @INC'  
  ```
  /Library/Perl/5.18/darwin-thread-multi-2level
  /Library/Perl/5.18
  /System/Library/Perl/5.18/darwin-thread-multi-2level
  /System/Library/Perl/5.18
  /System/Library/Perl/Extras/5.18/darwin-thread-multi-2level
  /System/Library/Perl/Extras/5.18
  . 
  ```
* /Network/Library 配下に Xcode 付属 Perl ライブラリのシンボリックリンクを作成  
  ```
  sudo mkdir /Network/Library
  sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl /Network/Library/Perl
  ```


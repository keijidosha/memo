# デバッグ

## クラッシュログを解析

* ~/.bash_profile に次の設定を追加  
export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer  
export PATH=$PATH:/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/PrivateFrameworks/DTDeviceKit.framework/Versions/A/Resources  
* 次のコマンドを実行  
symbolicatecrash <クラッシュファイルのパス> <dSYMのパス>  
それぞれのパスは次のディレクトリ以下にあります。  
  * クラッシュファイル  
~/Library/Logs/CrashReporter/MobileDevice/<デバイス名>/
  * dSYM  
~/Library/Developer/Xcode/DerivedData/<アプリケーション名/Build/
* Xcode4 インストール後に synbolicatecrash がうまく動いてくれない場合は、次のコマンドを実行  
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer/

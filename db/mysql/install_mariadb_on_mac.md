# MacにMariaDBをインストール

## インストール

### brew を使ってインストール(ports だと最新版の 10.x がインストールされなかった)

1. brew install mariadb
    ```
    A "/etc/my.cnf" from another install may interfere with a Homebrew-built
    server starting up correctly.
    
    To connect:
    mysql -uroot
    
    To have launchd start mariadb at login:
    mkdir -p ~/Library/LaunchAgents
    ln -sfv /usr/local/opt/mariadb/*.plist ~/Library/LaunchAgents
    Then to load mariadb now:
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mariadb.plist
    Or, if you don't want/need launchctl, you can just run:
    mysql.server start
    ==> Summary
    🍺 /usr/local/Cellar/mariadb/10.1.12: 565 files, 131.5M
    ```
1. mysql.server start
1. mysql_secure_installation  
=> ここで root のパスワードを設定

### ターミナルから起動・停止

* mysql.server stopf
* mysql.server start
* mysql.server restart

* バインドアドレスを 0.0.0.0 に変更
* バイナリーログのローテーションを設定
* 実行ユーザーを設定  
`vim /usr/local/opt/mariadb/homebrew.mxcl.mariadb.plist`
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>KeepAlive</key>
      <true/>
      <key>Label</key>
      <string>homebrew.mxcl.mariadb</string>
      <key>ProgramArguments</key>
      <array>
        <string>/usr/local/opt/mariadb/bin/mysqld_safe</string>
        <!--string>--bind-address=127.0.0.1</string-->
        <string>--bind-address=0.0.0.0</string>
        <string>--datadir=/usr/local/var/mysql</string>
        <string>--expire_logs_days=7</string>
      </array>
      <key>RunAtLoad</key> <true/>
      <key>WorkingDirectory</key>
      <string>/usr/local/var</string>
      <key>Disabled</key> <false/>
      <key>OnDemand</key> <false/>
      <key>UserName</key>
      <string>hoge</string>
    </dict>
    </plist>
    ```
  * KeepAlive プロセスが落ちても自動的に再起動
  * RunAtLoad OS 起動時にロード
  * OnDemand 要求された時にだけ実行 => 無効にして常に実行するようにする
  * UserName プロセスを実行するユーザ

## OS起動時に MariaDB を自動起動する場合

1. plistファイルのオーナーを root に変更  
`sudo chown root /usr/local/opt/mariadb/homebrew.mxcl.mariadb.plist`
* /Library/LaunchDaemons にシンボリックリンクを作成  
`sudo ln -sfv /usr/local/opt/mariadb/*.plist /Library/LaunchDaemons`
* plistファイルを自動起動対象に追加  
`sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.mariadb.plist`

## ログイン時に MariaDB を自動起動する場合

* ln -sfv /usr/local/opt/mariadb/*.plist ~/Library/LaunchAgents
* launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mariadb.plist  
または
* brew services start mariadb
* 自動起動の設定確認  
brew services list

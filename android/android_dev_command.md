# Android 開発コマンド

## エミュレータ

* SDカードイメージを指定してAVDを作成  
パス指定
* android create avd -n <AVD名> -t 2 -c <SDイメージファイルパス>  
サイズ指定
* android create avd -n <AVD名> -t 2 -c 512M  
* エミュレータ起動  
emulator -avd <AVD名>
* エミュレータに接続  
telnet localhost 5554
* リダイレクト(ホストに届いたパケットをエミュレータに転送)  
ホストの 1234ポートに届いたパケットをエミュレータの 5678 に転送。  
    ```redir add tcp:1234:5678```
* リダイレクトを解除  
redir del tcp:1234
* リダイレクトの一覧を表示  
redir list

## 位置情報
* 現在位置を設定  
geo fix 135.00 34.500

## adb

* シェル起動  
adb shell
* 接続されている実機の一覧を表示。  
adb devices
* エミュレータと実機の両方が存在する場合に、実機(デバイス)に対してコマンドを発行。  
adb -d shell
* エミュレータと実機の両方が存在する場合に、エミュレータに対してコマンドを発行。  
adb -e shell
* 複数の実機が接続されている場合に、シリアルNoを指定してコマンドを発行。  
adb -s <シリアルNo> shell
* コマンド実行  
adb shell ls -l /
* Activity 起動  
adb shell am start -n <Activity のパッケージ/.クラス名>  
(例) adb shell am start -n com.package/.Activity
* パッケージ一覧表示  
adb shell pm list packages
* パッケージインストール  
adb install -r xxx.apk  
-r:再インストール
* デバイスの状態を表示  
adb bugreport
* デバッグ接続可能なプロセスの一覧を表示  
adb jdwp
* 標準出力、エラー出力を使用可能に設定  
    adb shell stop
    adb shell setprop log.redirect-stdio true
    adb shell start

## ファイル転送

* android のファイルをローカルに転送  
adb pull /etc/wifi/wifi.conf .
* ローカルのファイルを android に転送  
adb push readme.txt <転送先android上のディレクトリ>

## ログ出力

* ログを継続表示  
adb logcat
* ログを一度だけ表示  
adb logcat -d
* ログレベルを指定して表示  
adb logcat *:E
* 指定可能なログレベル
  * V:Verbose
  * D:Debug
  * I:Information
  * W:Warning
  * E:Error
  * F:Fatal
  * S:Silent
* 特定のプログラムに対してログレベルを指定  
adb logcat *:E MediaPlayer:D
* ログの書式を指定  
adb logcat -v time
* 指定可能な書式
  * brief: デフォルト
  * process: プロセスIDのみ表示
  * tag: タグのみ表示
  * thread: スレッドIDも表示
  * raw: メッセージのみ表示
  * time: 発生日時を表示
  * long: すべて表示
* 出力するログバッファを指定  
adb logcat -b events
* ログバッファをクリア  
adb logcat -c
* ログバッファの容量を確認  
adb logcat -g
* ファイル経由せずにログを MacVim で表示  
adb logcat -c  
adb logcat -v time -d | Vim -g -

# APKファイル署名
    jarsigner -verbose -storepass <STOREファイルパスワード> -keypass <エイリアスパスワード> -keystore <キーストアファイル名> -signedjar <署名後のファイル名>.apk <署名するファイル名>.apk <キーペアのエイリアス>

-signedjar を省略すると、対象ファイルに署名して上書きされます。

## ビルド

* build.xml を生成  
  ```
    android update project -p <プロジェクトのパス> -n <プロジェクト名>  
  ```
  (例)  
  ```
    android update project -p /usr/local/android-ndk-r7/samples/hello-jni -n hello-jni --target 8
  ```
  AndroidManifest.xml に target 指定がない場合は、次のメッセージが表示されますので、target(Android SDK のバージョン)を指定します。  
  Error: The project either has no target set or the target is invalid.
* その後、ant を実行してビルドします。  
  ```
    adb clean debug
    adb clean release
  ```
* release ビルドする場合は、ant.properties をプロジェクトに作成して署名の情報を記述します。  
  ```
    key.store=/home/hoge/my.keystore
    key.alias=samples
    key.store.password=storepass
    key.alias.password=aliaspass
  ```

## JNI
* javah  
javah -classpath ../bin/classes -d . pkg.MyClassname
  * -classpath native メソッドを定義したクラスがビルドされたディレクトリ
  * -d ヘッダーファイルの出力先ディレクトリ

android.mk  
application.mk  
ndk-build

## zipalign

    zipalign -v 4 元apk 最適化後apk
(例)

    zipalign -v 4 sample_org.apk sample.apk

## apktool
    java -jar apktool.jar d 対象apk
(例)

    java -jar ~/apktool.jar d sample.apk

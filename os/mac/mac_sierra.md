# Maac: Sierra

## 古いアプリのインストール

古いアプリを起動しようとすると、「このアプリは壊れています。ゴミ箱に入れますか?」というダイアログが表示される。  
例えば muCommander 0.9.0 をインストールして実行しようとすると「Java6 が必要です」と表示される。  
そこで詳細情報をクリックすると、Safari で古い Java のダウンロードページが開く。  
古いJavaをインストールして起動すると、今度は壊れていますと表示される。  
そこで「セキュリティとプライバシー」で身元不明なアプリの実行を許可しようとしても、選択肢がなくなっている。  
(回避策)  
次のコマンドを実行する。  
`xattr -rc /Applications/xxx.app`

(参考) http://qiita.com/quattro_4/items/f5b56c1897c0cc235c0f

* ことえりでカタカナのショートカット control + shift + k を無効にする  
  Atom の行削除とショートカットが衝突するため。  
  Atom で行削除すると入力モードがカタカナに切り替わってしまうのを避けるため。  
  1. Mac起動時に Command + R を押し、リカバリーモードで起動する。
  1. メニューからターミナルを開く。
  1. csrutil disable を実行し、rootless モードを解除。
  1. 再起動。
  1. /System/Library/Input Methods/JapaneseIM.app/Contents/PlugIns/JapaneseIM.appex/Contents/Resources/KeySetting_Default.plist を別のディレクトリにコピーしてバックアップ。
  1. vi /System/Library/Input Methods/JapaneseIM.app/Contents/PlugIns/JapaneseIM.appex/Contents/Resources/KeySetting_Default.plist
  1. 1673行目からの次の行をコメントアウトして保存する。
     ```
     <!--
                 <key>shift+control+&apos;k&apos;</key>
                 <dict>
                     <key>command</key>
                     <string>switch_to_katakana_mode</string>
                     <key>menu_item</key>
                     <true/>
                 </dict>
     -->
     ```
  1. ことえりを再起動して、カタカナのショートカットが無効になっていることを確認する。  
     アクティビティモニタで「日本語入力プログラム」を終了し、起動してくるまで待ってから、メニューバーの日本語入力アイコンをクリックして確認。
  1. 再起動し、Command + R でリカバリーモードで起動する。
  1. メニューからターミナルを開く。
  1. csrutil enable を実行。
  1. 再起動。

## FreeMind でノードが編集できない

/Applications/FreeMind.app/Contents/Info.plist の次の 2行をコメントアウトする。
```
<!--
<key>JVMRuntime</key>
<string>jdk1.7.0_45.jdk</string>
-->
```
(参考) https://freemind.asia/faqs/mac/sierra.html




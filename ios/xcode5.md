# Xcode5

## Xcode5 で iOS6.1 SDK を使って開発

* シンボリックリンク作成
  ```
  cd /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs
  ln -s /Applications/Xcode4.6.3.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS6.1.sdk .
  ```
* Target の Base SDK で iOS 6.1 を選択
* 実機を使って実行  
  実行する端末名が 2行表示される  
  上が iOS6.1、下が iOS7.0 の SDK を使ってビルドされるらしい。  
  ![xcode menu](ios/images/xcode5_screenshot.png)

2013.10.30追記)
この方法で試したところ、次の問題が発生。
* iOS6.1端末をUSBでつないでデバッグ実行し、ブレークポイントで止めてステップ実行するとアプリがSEGVでクラッシュ。

## Xcode 5で追加されたコメント記法

```
/*! このメソッドの説明
 * \param パラメーターの説明
 * \return 戻り値の説明
 */
- (void)xxx
{
}
```

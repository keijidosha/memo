# Cocoa Touch Static Library プロジェクトで実機とシミュレータ用のユニバーサルスタティックライブラリを作成

1. ビルド対象に iPhone 5.1 Simulator を選択してビルド(command + B)
1. ビルド対象に iOS Device を選択して、Build for Archive を実行
1. それぞれにビルドされたライブラリを結合
`lipo ~/Library/Developer/Xcode/DerivedData/xxx/Build/Products/Release-iphoneos/libXXX.a ~/Library/Developer/Xcode/DerivedData/xxx/Build/Products/Debug-iphonesimulator/libXXX.a -create -output ~/iPhoneApp/lib/libXXX.a`

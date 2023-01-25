# PJSIP

## PJSIPをiPhone用にビルド

### PJSIP 1.12 + Xcode 4.3

* 環境変数設定
  ```
  export DEVPATH=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer
  export CC=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/arm-apple-darwin10-llvm-gcc-4.2
  # OpenSSL とのリンク用
  export OPENSSL=/Users/hoge/build/src/ssllibs
  export CFLAGS="-O2 -Wno-unused-label -I${OPENSSL}/include"
  export LDFLAGS="-L${OPENSSL}" 
  ```
* user.mak作成  
  vi user.mak
  ```
  # for "ld: warning: CPU_SUBTYPE_ARM_ALL subtype is deprecated:"
  export CFLAGS += -march=armv7 -mcpu=arm1176jzf-s -mcpu=cortex-a8
  export LDFLAGS += -march=armv7 -mcpu=arm1176jzf-s -mcpu=cortex-a8
  ```
* config_site.h作成(編集)  
  vi pjlib/include/pj/config_site.h
  ```
  #define PJ_CONFIG_IPHONE 1
  #include <pj/config_site_sample.h>
  ```
* ビルド  
  ```
  ./configure-iphone
  make dep
  make
  ```

#### Xcodeで新規プロジェクト作成

* フレームワーク追加
  * CFNetwork.framework
  * AudioToolbox.framework
* PJSIPへの参照追加
  * libg7221codec-arm-apple-darwin9.a
  * libilbccodec-arm-apple-darwin9.a
  * libmilenage-arm-apple-darwin9.a
  * libpjsdp-arm-apple-darwin9.a
  * libspeex-arm-apple-darwin9.a
  * libsrtp-arm-apple-darwin9.a
  * libgsmcodec-arm-apple-darwin9.a
  * libpj-arm-apple-darwin9.a
  * libpjlib-util-arm-apple-darwin9.a
  * libpjmedia-arm-apple-darwin9.a
  * libpjmedia-audiodev-arm-apple-darwin9.a
  * libpjmedia-codec-arm-apple-darwin9.a
  * libpjnath-arm-apple-darwin9.a
  * libpjsip-arm-apple-darwin9.a
  * libpjsip-simple-arm-apple-darwin9.a
  * libpjsip-ua-arm-apple-darwin9.a
  * libpjsua-arm-apple-darwin9.a
  * libresample-arm-apple-darwin9.a
* あらかじめビルドしておいた OpenSSL への参照を追加
  * libcrypto.a
  * libssl.a
* Build Settings に設定を追加
  * Apple LLVM compiler 3.1 - Preprocessing  
    「PJ_AUTOCONF=1」設定を追加  
    これを追加しないと次のビルドエラーが発生します。  
    「#error Endianness must be declared for this processor」  

### pjsipをiOSシミュレータ用にビルド

環境: Xcode 4.4.1

* configure-iphone を編集し、次の2行を追加
  ```
  DEVPATH=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer
  IPHONESDK=iPhoneSimulator5.1.sdk
  ```
* 次の環境変数を設定  
  ```
  export CC=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/usr/bin/gcc
  export CFLAGS="-O2 -m32 -miphoneos-version-min=5.0 -g -ggdb -g3 -DNDEBUG"
  export LDFLAGS="-O2 -m32"
  ```
* ビルド
  1. ./configure-iphone
  1. make dep
  1. make clean
  1. make

ビルドされたライブラリを arm 用と結合してユニバーサルバイナリを作成
```
export TARGET_DIR=~/pjsip/universal
export I386_DIR=~/pjsip/simulator
export ARM_DIR=~/pjsip/arm

TARGET=pjlib; \
FILE="libpj-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done

TARGET=pjlib-util; \
FILE="libpjlib-util-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done

TARGET=pjmedia; \
FILE="libpjmedia-arm-apple-darwin9.a libpjmedia-audiodev-arm-apple-darwin9.a libpjmedia-codec-arm-apple-darwin9.a libpjsdp-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done

TARGET=pjnath; \
FILE="libpjnath-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done

TARGET=pjsip; \
FILE="libpjsip-arm-apple-darwin9.a libpjsip-simple-arm-apple-darwin9.a libpjsip-ua-arm-apple-darwin9.a libpjsua-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done

TARGET=third_party; \
FILE="libg7221codec-arm-apple-darwin9.a libgsmcodec-arm-apple-darwin9.a libilbccodec-arm-apple-darwin9.a libmilenage-arm-apple-darwin9.a libresample-arm-apple-darwin9.a libspeex-arm-apple-darwin9.a libsrtp-arm-apple-darwin9.a"; \
for f in $FILE; do lipo $ARM_DIR/$TARGET/lib/$f $I386_DIR/$TARGET/lib/$f -create -output $TARGET_DIR/$f; done
```

(参考)  
http://stackoverflow.com/questions/11848333/just-info-how-to-build-and-compile-pjsip-for-xcode-using-sample-code-ipjsua-t


# Linux part 2


## Tips

### システム日時を偽装する(プログラムテスト用などに)(CentOS向け)
#### 環境構築

1. yum install git gcc make
1. git clone https://github.com/wolfcw/libfaketime.git
1. cd libfaketime/src
1. make install
1. echo -e '/usr/local/lib/faketime/libfaketime.so.1' > /etc/ld.so.preload
1. export DONT_FAKE_MONOTONIC=1
1. export FAKETIME_CACHE_DURATION=1
1. export DONT_FAKE_MONOTONIC=1  
これを指定しておかないと Java でエラーが発生する?

#### プログラムから見た日時を変更
* 指定した日時にを変更する  
  echo -e '@2000-01-01 09:00:00' > /etc/faketimerc
* システム日時から指定した日数ずらす  
echo -e '+3d' > /etc/faketimerc
* システム日時から指定した年数ずらす  
echo -e '-10y' > /etc/faketimerc
* 元に戻す  
rm -f /etc/faketimerc

(参考) [システム日付を変更したコンテナ環境でテストしたい](https://qiita.com/Targityen/items/67682d6c80cdcbe1186c)


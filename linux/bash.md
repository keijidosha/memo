Table of Contents
=================

* [日付](#日付)
   * [日付を取得する](#日付を取得する)
   * [日付(時刻)をファイル名にする](#日付時刻をファイル名にする)
   * [Mac で複数のファイルを一括でタイムスタンプを取得し、年月日時分秒形式に変換してファイル名にセットする](#mac-で複数のファイルを一括でタイムスタンプを取得し年月日時分秒形式に変換してファイル名にセットする)
* [計算する](#計算する)
   * [10進数計算する](#10進数計算する)
      * [expr を使う](#expr-を使う)
      * [$(()) を使う](#-を使う)
      * [bc を使う](#bc-を使う)
   * [16進数 ⇔ 10進数計算する](#16進数--10進数計算する)
* [文字列置換](#文字列置換)
* [正規表現](#正規表現)
* [ループ](#ループ)
* [その他](#その他)
   * [shell script samples](#shell-script-samples)
* [Java](#java)

## 日付

### 日付を取得する

* 今日の日付  
date +%Y%m%d
* 現在の日付時刻  
date +%Y%m%d%H%M%S
* Mac
  * 昨日の日付  
  date -v-1d +%Y%m%d
  * 明日の日付  
  date -v+1d +%Y%m%d
  * 今月1日  
  date -v1d +%Y%m%d
  * 翌月1日  
  date -v+1m -v1d +%Y%m%d
  * 今月末日  
  date -v+1m -v1d -v-1d +%Y%m%d
  * 指定した日付から90日前  
  date -v+90d -j -f %Y%m%d 20190720 +%Y%m%d
  * 指定した日付から90日後  
  date -v-90d -j -f %Y%m%d 20190720 +%Y%m%d
* Linux
  * 昨日の日付  
  date --date "1 days ago" +%Y%m%d
  * 明日の日付  
  date --date "1 days" +%Y%m%d
  * 今月1日  
  date --date "today" +%Y%m01
  * 翌月1日  
  date --date "next month" +%Y%m01
  * 今月末日  
  date --date "`date --date "next month last day" +%Y%m01` last day" +%Y%m%d
  * 指定した日付から90日前  
  date --date "20190720 90 days ago" +%Y%m%d
  * 指定した日付から90日後  
  date --date "20190720 90 days" +%Y%m%d
  * UNIX時間を表示  
  date +%s
  * 指定した日時の UNIX時間を表示  
  date --date "2019-07-20 09:15:30" +%s

### 日付(時刻)をファイル名にする
    today=`date +%Y%m%d.txt`
    echo hoge > ${today}
    
    now=`date +%Y%m%d%H%M%S.txt`
    echo hoge > ${now}

### sudo で I/O リダイレクト
I/Oリダイレクトして root 権限が必要にファイルに書き込む

* 書き込む  
  * (通常)  
`ls -l > rootfile.txt`
  * (sudoあり)  
`ls -l | sudo tee rootfile.txt`
* 追記
  * (通常)  
`ls -l >> rootfile.txt`
  * (sudoあり)  
`ls -l | sudo tee -a rootfile.txt`


### Mac で複数のファイルを一括でタイムスタンプを取得し、年月日時分秒形式に変換してファイル名にセットする
    for f in *.txt; do ts=`stat -f %m $f`; dt=`date -r $ts +%Y%m%d-%H%M%S`; mv $f $dt.jpg; done

## 計算する

### 10進数計算する

#### expr を使う
`expr 3 "*" 5`
#### $(()) を使う
`echo $((3 * 5))`
#### bc を使う
echo "3 * 5" | bc
または bc コマンドを実行して、対話的に計算式を入力

### 16進数 ⇔ 10進数計算する

* 10進数 => 16進数  
printf '0x%x\n' 255
* 16進数 => 10進数  
printf '%d\n' 0xff

## 空行を削除
* grep -v '^$'
* sed '/^$/d'
* grep .

## 文字列置換

* ${parameter#word}      先頭から前方最短一致した位置まで取り除きます
* ${parameter##word}    先頭から前方最長一致した位置まで取り除きます
* ${parameter%word}     末尾から後方最短一致した位置まで取り除きます
* ${parameter%%word}  末尾から後方最長一致した位置まで取り除きます
* ${parameter/before/after}  最初に一致した文字列を置換します
* ${parameter//before/after}  一致した文字列をすべて置換します

## 正規表現
正規表現文字列をダブルクォートで囲むことはできない  
\s のような文字クラスは使えない  
```bash
VAR="ABC123"
if [[ $VAR =~ ^([A-z]+)([0-9]+)$ ]]; then
  echo ${BASH_REMATCH[1]}
  echo ${BASH_REMATCH[2]}
fi
```

スペースを含む場合は、正規表現文字列を変数にセットしてマッチング処理ができる  
タブ文字を入力する場合は control + V, control + I で入力  
```bash
VAR="ABC 123"
REG="^([A-z]+)[ ]*([0-9]+)$"
if [[ $VAR =~ $REG ]]; then
  echo ${BASH_REMATCH[1]}
  echo ${BASH_REMATCH[2]}
fi
```

## ループ
10 回繰り返す
```bash
for i in {1..10}
do
    echo $i
done
```

変数に格納された回数繰り返す
```bash
MAX=10
for i in `seq 1 $MAX`
do
    echo $i
done
```

ファイルを 1行ずつ読み込んで処理する
```bash
while read line
do
    echo $line
done < readme.txt
```

標準入力から 1行ずつ読み込んで処理する
```bash
ls -1 | while read line; do echo $line; done
```

ワンライナー(ループ内)でバッグラウンド実行する  
```bash
for F in *.json; do hoge $F &; done
```
このようにセミコロン(;)の前に & があると「-bash: syntax error near unexpected token `;'」というエラーになる。  
(解決策)  
次のようにセミコロン(;)と & の間に何かコマンドを入れておく。
```bash
for F in *.json; do hoge $F & sleep 0; done
```


## その他
#### shell script samples
* find を実行した結果、見つかったファイルの行数が 1行以上の場合だけ、条件が成立し、処理を実行。  
    ```
    if [ `find /tmp/foo -name '*.txt' -type f | wc -l` -gt 0 ] ; then
        # 処理
    fi
    ```
* ファイル名を正規表現で一括変換する  
  (例) 年月日の間にピリオドを挿入する。つまり、20100301.txt を 2010.03.01.txt に変換する。
    ```
    for file in 20*; do mv $file `echo $file | sed -e "s/\([0-9]\{4\}\)\([0-9]\{2\}\)\([0-9]\{2\}\)/\1\.\2\.\3/"`; done
    ```
    echoで実行内容を表示し、正しく変換できているのを確認してから実行すると良い。
    ```
    for file in 20*; do echo `echo $file | sed -e "s/\([0-9]\{4\}\)\([0-9]\{2\}\)\([0-9]\{2\}\)/\1\.\2\.\3/"`; done
    ```
* 前日を取得  
`date –date '1 day ago'`  
  (例) 前日の日付を8桁の数字でシェル変数 yesterday にセット 
    ````
    yesterday=`date -d '1 day ago' +%Y%m%d`
    ````
* for を使って複数のファイルに対する操作を実行  
  (例) 拡張子を doc から txt に変更
    ```
    for fname in *.doc; do mv $fname ${fname%.doc}.txt; done
    ```
  (例) すべての JAR ファイルに署名
    ```
    for jar in *.jar; do jarsigner -keystore /mnt/foo.key -storepass passs -keypass passk -signedjar sign/$jar $jar aliasname; done
    ```
* 重複したコマンドを実行履歴に格納しない。  
次の行を ~/.bash_profile に追加  
`HISTCONTROL=ignoredups`
* ctrl + r でコマンド履歴を戻りすぎた時、ctrl + s で逆方向に進める設定  
次の行を ~/.bash_profile に追加  
`stty stop undef`
* プロンプトに日付・時刻を表示する  
  ```
  export PS1="[\u@\h\D{ %FT%T} \W]\\$ "
  ```  
  export PS1="[\u@\h \D&#x7B;%FT%T} \W]\\$ "  
  ⬆︎"&#x7B;%" のエスケープがうまくいかないので '{' を 16進数で記述。

## Java
* RedHat Linux 9 で JDK 1.3.1 を使う場合。  
環境変数 LD_ASSUME_KERNEL=2.2.5 を指定。

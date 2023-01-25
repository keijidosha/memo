# tar

* tar ファイルに圧縮後、圧縮したファイルを削除。  
    ```
    tar zcvf xxx.tar.gz –remove-files *.txt
    ```
* tar ファイルに格納されたファイルの内容を画面に表示  
  * すべてのファイルを画面に表示
    ```
    tar zxOf xxx.tar.gz
    ```
  * 特定のファイルを画面に表示  
    ```
    tar zxOf xxx.tar.gz foo.txt
    ```
  (ワイルドカード指定はダブルクォートで囲む)
    ```
    tar zxOf xxx.tar.gz “*.txt”
    ```
* vi, less を使って tar に格納されたファイルを表示  
    ```
    tar zxOf xxx.tar.gz “*.txt” | less
    tar zxOf xxx.tar.gz “*.txt” | view -
    tar zxOf xxx.tar.gz “*.txt” | vi -R -
    ```
* ルートディレクトリから属性付きで圧縮する  
`tar zcvfPp xxx.tar.gz /xxx /zzz`  
P: ディレクトリ先頭の '/' を取り除かない  
p: 属性を維持する(スーパーユーザーのデフォルト)  
  * ルートディレクトリからの解凍は次のようにする  
`tar zxvfPp xxx.tar.gz`
* tar で解凍する前に、tar に含まれているファイルをバックアップする  
`tar ztf /tmp/new.tar.gz | sed 's/^\(..*\)$/\/\1/' | sed -e '/\/$/d' | tar -zcv -T - -f /tmp/old.tar.gz`  
tf で表示されたパスの先頭に / を追加する  
tf で表示されたパスの最後が / で終わっている(つまりディレクトリ)場合は、最後の / を削除  

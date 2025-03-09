- Table of Content  
{:toc}

# sed

* 置換
  ```
  sed -e "s/^123$/456/"
  ```
  * 置換後の文字列に /(スラッシュ)を含む場合、\(バックスラッシュ)でエスケープ
    ```
    sed -e "s/^hoge$/http:\/\/hoge.com\//"
    ```
  * パターンにマッチした文字列を展開して置換  
    * \1, \2, ... で展開  
      ```
      echo "   ID  =  abc" | sed -e "s/\( *ID *= *\)abc/\1def/"
      ```  
      結果: "   ID  =  def"
  * 複数行にまたがって置換  
    * 2行を N で行結合してから置換  
      ```
      echo -e "[config]\nname=user" | sed "N; s/\[config\]\nname=[A-z]*/[config]\nname=hoge/"
      ```
      結果:
      ```
      [config]
      name=hoge
      ```
    * インプットが 3行以上ある場合? は、:loop; P; D も必要?
      ```
      echo -e "#comment\n[config]\nname=user\n#comment" | sed ":loop; N; s/\[config\]\nname=[A-z]*/[config]\nname=hoge/g; P; D"
      ```
      結果:
      ```
      #comment
      [config]
      name=hoge
      #comment
      ```
    * 置換対象が 3行以上にまたがる場合は -z が有効か?
      ```
      echo -e "[config]\nname=user" | sed -z "s/\[config\]\nname=[A-z]*\nage=[0-9]*/[config]\nname=hoge\nage=15/"
      ```
    * 間に複数行はさんだ下にある行を置換  
      ```
      # db configurtion
      [db]
      name=hoge
      id=hoge
      password=password
      ip=127.0.0.1
      readonly=true
      ```
      * [db] から 4行以内にある ip= で始まる行を 192.168.1.1 に置換。
        ```
        echo -e "# db configurtion\n[db]\nname=hoge\nid=hoge\npassword=password\nip=127.0.0.1\nreadonly=true" | sed "/\[db\]/,+4 s/ip=..*/ip=192.168.1.1/"
        ```
        結果
        ```
        # db configurtion
        [db]
        name=hoge
        id=hoge
        password=password
        ip=192.168.1.1
        readonly=true
        ```
      * [db] から 3〜5行目の範囲にある、ip= で始まる行を 192.168.1.1 に置換。  
        この場合は awk を使用(sed で検索結果からの範囲指定がうまくいかないため)。
        ```
        echo -e "# db configurtion\n[db]\nname=hoge\nid=hoge\npassword=password\nip=127.0.0.1\nreadonly=true" | awk "/\[db\]/ { c=0 } c>=3 && c<=5 { gsub(/ip=.*/, \"ip=192.168.1.1\") } { c++; print }"
        ```
      * `([^\n]\n){行数}` で行数をカウント  
        (例) [db] から 3〜5行の範囲にある行の IP を 127.0.0.1 から 192.168.1.1 に置換  
        sed: 3〜5行を `\([^\n]*\n\)\{3,5\}` で表現。  
        ```
        echo -e "# db configurtion\n[db]\nname=hoge\nid=hoge\npassword=password\nip=127.0.0.1\nreadonly=true" | sed -z "s/\(\[db\]\n\([^\n]*\n\)\{3,5\}\)ip=[^\n]*/\1ip=192.168.1.1/"
        ```
* 行削除
  ```
  sed -e "/^hoge.*$/d"
  ```
* ファイルを上書きして更新  
  -i を使用
  ```
  sed -i -e "s/^123$/456/"
  ```

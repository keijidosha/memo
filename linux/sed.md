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
    \1, \2, ... で展開  
    ```
    echo "   ID  =  abc" | sed -e "s/\( *ID *= *\)abc/\1def/"
    ```  
    結果: "   ID  =  def"
  * 2行にまたがって置換  
    N で行結合してから置換  
    ```
    echo -e "[config]\nname=user" | sed "N; s/\[config\]\nname=[A-z]*/[config]\nname=hoge/"
    ```
    結果:
    ```
    [config]
    name=hoge
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

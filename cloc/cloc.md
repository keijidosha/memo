- Table of Content  
{:toc}

# cloc

* 複数の git リポジトリをまとめて一括で、タグ間の差分を比較
  1. タグ用のディクレトリを作成
     ```
     mkdir tag1 tag2
     ```
  1. タグごとに比較対象のリポジトリをチェックアウト
     ```
     cd tag1
     git clone git@github.com:hoge/hoge1.git -b tag1
     git clone git@github.com:hoge/hoge2.git -b tag1
     cd ../tag2
     git clone git@github.com:hoge/hoge1.git -b tag2
     git clone git@github.com:hoge/hoge2.git -b tag2
     cd ..
     ```
  1. タグごとに比較対象ディレクトリの一覧を記述したファイルを作成  
     tag1.txt  
     ```
     tag1/hoge1
     tag1/hoge2
     ```
     tag2.txt  
     ```
     tag2/hoge1
     tag2/hoge2
     ```
  1. cloc で作成したファイルをパラメーター指定して比較
     ```
     cloc --diff-list-files tag1.txt tag2.txt
     ```



# Ruby: ファイル

## 読み書き

* コマンドラインに指定されたファイルをオープンして 1行ごとに読み込む  
  ```
  filename = ARGV[0]
  open(filename) { |f|
      while line = f.gets do
          print line
      end
  }
  ```
* 引数指定されたファイルから一括読み込み  
  ```
  filename = ARGV[0]
  data = File.open(filename).read
  ```
* 標準入力から1行ずつ読み込む  
  ```
  while line = STDIN.gets
      print line
  end
  ```
* 標準入力から一括読み込み  
`data = STDIN.read`
* ファイルから一括読み込み  
  ```
  str = open("hoge.txt", &:read)
  str = File.read("hoge.txt")
  ```
* 文字列をファイルに書き込み  
`File.write("hoge.txt", "hoge\n")`
* ディレクトリにあるファイルを順に処理する  
  ```
  Dir::glob("dir/*.txt").each{|file|
  }
  ```
* オブジェクト構造をデバッグ用にファイル出力  
  ```
  require 'pp'
  File.open("./debug.txt", "w"){ |f|
     f.write object.pretty_inspect
  }
  ```

## ファイル操作

* 存在チェック  
  ```
  if File.exist?('filename.txt')
    # ファイルが存在する時の処理
  else
    # ファイルが存在する時の処理
  end
  ```
* パスがファイルかディレクトリかチェック  
  ```
  if FileTest.file?( '/hoge/path' )
    # ファイルだった場合の処理
  elsif FileTest.directory?( '/hoge/path' )
    # ディレクトリだった場合の処理
  end
  ```



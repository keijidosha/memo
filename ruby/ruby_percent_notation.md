# Ruby %記法

1. %、%Q  
   ダブルクオートで囲う場合と同等。  
   シングルクオートやダブルクオートのエスケープが不要になる。  
   後述の%q()と違い、変数・定数の展開もできる。  
   ```
   str = %(Programming language "Ruby")
   puts str
   # => Programming language "Ruby"
   ```
   ```
   ruby = "Ruby"
   str2 = %(Programming language "#{ruby}")
   puts str2
   # => Programming language "Ruby"
   ```
1. %q  
   シングルクオートで囲う場合と同等。  
   シングルクオートなので、変数や定数の展開はされない。  
   ```
   ruby = "Ruby"
   str = %q(Programming language "#{ruby}")
   puts str
   # => Programming language "#{ruby}"
   ```
1. %w  
   配列を作る。配列の要素はスペース区切りで指定する。  
   式の展開はされない。  
   ```
   array = %w(one two three four)
   p array
   # => ["one", "two", "three", "four"]
   ```
1. %W  
   配列を作る。%w()と違い、式の展開がされる。  
   ```
   ruby = 'Ruby'
   PYTHON = 'Python'
   
   array = %W(#{ruby} #{PYTHON} PHP)
   p array
   # => ["Ruby", "Python", "PHP"]
   ```
1. %i  
   要素がシンボルの配列を作る。  
   ```
   array = %i(Ruby Python PHP)
   p array
   # => [:Ruby, :Python, :PHP]
   ```
1. %I  
   要素がシンボルの配列を作る。%i()と違い、式の展開がされる。  
   ```
   ruby = 'Ruby'
   PYTHON = 'Python'
   
   array = %I(#{ruby} #{PYTHON} PHP)
   p array
   # => [:Ruby, :Python, :PHP]
   ```
1. %x  
   コマンド出力を行う。バッククオートで囲った場合と同等。  
   ```
   res = %x(date) # dateコマンドの実行
   puts res
   # => Sat Aug 23 23:27:01 JST 2014
   ```
1. %s  
   シンボル。式の展開はされない。  
   ```
   sym = %s(Ruby)
   p sym
   # => :Ruby
   ```

[Rubyで%記法（パーセント記法）を使う](https://qiita.com/mogulla3/items/46bb876391be07921743)

# Ruby: クラス、メソッド、変数

## クラス

### クラスの情報を取得

* インスタンスメソッド  
hoge.class.instance_methods
* public なメソッドの一覧  
hoge.class.public_methods
* 継承元  
hoge.class.ancestors
* インスタンスが特定のクラスを継承しているか判定  
  ```
  "hoge".kind_of?(String)  # => true
  123.kind_of?(Integer)   # => true
  ```
* インスタンスが特定のクラスであるかを判定  
  ```
  "hoge".instance_of?(String)  # => true
  123.instance_of?(Integer)   # => false
  123.kind_of?(Fixnum)   # => true
  ```

### その他

* インスタンスからクラス名を取得(名前空間付き)  
classname = instance.class.name
* 名前空間付きクラス名から名前空間を取り除く  
classname_without_namespace = classname.demodulize


## メソッド

* クラスメソッド定義
  ```
  class Hoge
    self.def fuga
      ...
    end
  end
  
  Hoge.fuga
  ```
* インスタンス変数へのアクセス定義
  * accessorを使用
    ```
    class Hoge
      attri_accessor :foo
    end
    
    hoge = Hoge.new
    hoge.foo = 123
    puts hoge.foo
    ```
  * メソッドを定義
    ```
    class Hoge
      def foo
        @foo
      end
    
      def foo=(val)
        @foo = val
      end
    end
    
    hoge = Hoge.new
    hoge.foo = 123
    puts hoge.foo
    ```

### 引数

* 引数にデフォルト値を指定
  ```
  def hoge(name, no=1, option=true)
    ...
  end
  ```
* オプション引数をハッシュで渡す
  ```
  def hoge(name, opt = {})
    no = opt[:no]
    flag = opt[:flag]
  end
  
  hoge( "fuga", no: 1, flag: false )
  ```
* キーワード引数
  ```
  def hoge(name, no: 1, flag: true)
    puts "#{name}, #{not}, #{flag}"
  end
  
  hoge( "smith", flag: false )
  ```
* 可変引数
  ```
  def hoge( *param )
    param.each do |p|
      puts p
    end
  end
  
  hoge( 'hello', 1, true )
  ```
  \* 1個は、パラメーターは配列で渡る
  ```
  def hoge( **param )
    param.each do |key, val|
      puts "#{key} = #{val}"
    end
  end
  
  hoge( msg: 'hello', no: 1, flag: true )
  ```
  \**(*2個)は、パラメーターはハッシュで渡る
* ブロック引数
  ```
  def hoge( array )
    array.each do |ary|
      yield ary if block_given?
    end
  end
  
  # または
  
  def hoge( array, &block )
    array.each do |ary|
      block.call( ary ) if block
    end
  end
  
  hoge( [1,3,5] ) { |x| puts x }
  ```


## 変数

### 変数スコープ

* インスタンス変数  
@で始まる  
@hoge  
* クラス変数  
@@で始まる  
@@hoge  
* ローカル変数  
小文字、または _ で始まる  
hoge, _hoge  
* グローバル変数  
$ で始まる  
$hoge  
* 定数  
大文字で始まる  
HOGE, Hoge  

### 空チェック

* nil?  
インスタンスが未設定  
* empty?  
  インスタンスは nil でないが、内容が空  
  * 空文字列: ""  
  * 空配列: []  
  * 空ハッシュ: {}  
  * blank?  
Rails ActiveRecord の拡張(require 'active_record')  
nil? || empty?  
空白文字: " " も True  
* present?  
Rails ActiveRecord の拡張(require 'active_record')  
blank? の逆  
unless hoge.blank? と if hoge.present? は同じ意味  

### 定数

* 文字列から定数の値を取得する  
  (例) mail gem の smtp.rb から抜粋
  ```
  openssl_verify_mode = "none"
  
  if openssl_verify_mode.kind_of?(String)
    openssl_verify_mode = "OpenSSL::SSL::VERIFY_#{openssl_verify_mode.upcase}".constantize 
  end
  ```

### enum

* enum を module の定数として定義
  ```
  module Status
      NEW = 0
      UPDATED = 1
      DELETED = 2
  
      STATUS_NAME = {
          Status::NEW => "新規"
          Status::UPDATED => "更新"
          Status::DELETED => "削除"
      }
  end
  ```


### その他

* クラスに定義されたインスタンス変数を公開する(accessor を用意する)  
attr_accessor :<インスタンス変数名1>, :<インスタンス変数名2>  
※シンボル(変数名)に @ は付けない  
* コマンドライン引数を取得  
ARGV[0], ARGV[1], ...  
* ハッシュパラメーターからインタタンス変数に値をセット  
  ```
  def initialize(opts = {})
    opts.each {|k, v| instance_variable_set("@#{k}", v) }
  end
  ```
* インスタンス変数からハッシュを作成
  ```
  hash = {}.tap {|h|
    me.instance_variables.each { |sym|
      h[sym] = me.instance_variable_get(sym)
    }
  }
  ```


## その他

メソッドを定義せず、Proc を使い、複数の値に対して同じ処理をさせる(内部サブルーチン的な使い方)
```
[ [ 1, "spring"],
  [ 2, "summer"],
  [ 3, "autumn"],
  [ 4, "winter"]
].map { |param|
  Proc.new{|id, name|
    puts "#{id}: #{name}"
  }.call(param[0], param[1])
}
```



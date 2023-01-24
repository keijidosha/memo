# Ruby

## 実行

### ロードパス、Rubyファイルのロード

* ロードパスにないRubyファイルのロード
  * Rubyソースファイルがある場所から相対パスで fuga/Hoge.rb をロード  
    `require_relative 'fuga/hoge'`
  * ロードパスにカレントディレクトリを追加して、fuga/Hoge.rb をロード
    ```
    $LOAD_PATH << "."
    require 'fuga/hoge'
    ```
* Rails のライブラリーを使って(参照して) Ruby を実行  
  ```
  cd <Rails Root>
  bundle exec ruby hoge.rb
  ```
* ロードされたファイルの一覧  
$LOADED_FEATURES 変数を参照  
`puts $LOADED_FEATURES`


## その他

### 乱数

* 乱数を生成する
  * 1から10までの乱数  
    `[*1..10].sample`  
    または
    `rand(10)+1`
  * 1,3,5,7,9 の乱数  
    `[1,3,5,7,9].sample`
  * 1と2の乱数で、1が出る確率を高くする  
    `[1,1,2].sample`


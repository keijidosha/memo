# Rails

## 環境構築

特定のディレクトリ以下だけに rails の gem も含めてインストール

```
mkdir test
cd test

# プロジクト用 ruby 環境作成
vim setenv.sh
------------------------------------------------------------
PROJECT_HOME=/Users/hoge/test
export GEM_HOME=$PROJECT_HOME/gems/gems
export RUBYLIB=$PROJECT_HOME/gems/lib
export RUBY_USER_INSTALL=true
export PATH=$PATH:$PROJECT_HOME/gems/gems/bin
------------------------------------------------------------
. setenv.sh
gem install bundler
gem install bundle

# libxml2 は nokogiri のビルドに使用
brew install libxml2 libxslt libiconv
# brew 以下の libxml2 を使って nokogiri をビルドするように設定
bundle exec bundle config build.nokogiri --use-system-libraries --with-iconv-dir="$(brew --prefix libxml2)"
rbenv rehash

# Railsインストール用 bundler をインストール
vim Gemfiledd
------------------------------------------------------------
source "http://rubygems.org"
gem "rails", "4.2.7"
------------------------------------------------------------
bundler install --path vender/bundle

# Railsプロジェクト作成
bundle exec rails new test2 --skip-bundle
# Railsインストールに使用した bundler を削除
rm -f Gemfile
rm -f Gemfile.lock
rm -rf .bundle
rm -rf vendor/bundle

# プロジェクト用 Railsインストール
cd test2
bundle install --path vendor/bundle
echo '/vendor/bundle' >> .gitignore
bundle exec rails server
```

(参考)  
Rails開発環境の構築（複数バージョン共存可能）（Homebrew編）  
http://qiita.com/emadurandal/items/e43c4896be1df60caef0  
nokogiriとlibxml2とhomebrew  
http://qiita.com/ohataken/items/9d6d4e31615021cf016d  
Mac OS X Mavericksでrailsのbundle install時にnokogiriインストールエラー  
http://qiita.com/kytiken/items/45da8f61775ec1cff3f8  

## routes.rb

### resources で定義されるアクション

* index
* new
* create
* show
* edit
* update
* destroy

有効な routes は rake routes または  
http://localhost:3000/rails/info/routes で確認可能(Rails 4 以降)

## その他

* Rails のルートディレクトリパスを取得  
Rails.root(v3以降)
* Rails で実行中かを判定
  ```
  if ENV['RAILS_ENV'].nil?
    # Rails実行でない(コマンドラインから実行?)
  else
    # Rails で実行
  end
  ```
* development モードで実行中かを判定
  ```
  if Rails.env.development?
    # development モードでの処理
  end
  ```
* Controller 以外のクラスから logger を参照  
`Rails.application.config.logger`
* 現在実行しているスクリプトのパスを取得  
`path = __FILE__`
* Rails のコンソール実行中に、修正されたクラスをリロードする  
reload! を実行  
  ```
  bin/rails console
  Loading development environment (Rails 4.2.4)
  2.2.3 :001 > reload!
  Reloading...
  => true 
  2.2.3 :002 >
  ```
* Webrick を production モードで起動。  
`SECRET_KEY_BASE=$(rake secret) bin/rails server -eproduction`
* コントローラーやモデルから View のヘルパーメソッドを呼び出す。  
view_context を使う。  
(例)  
`view_context.number_to_human_size(1024)`
コンソールから呼び出す場合は、helper を使う。  
(例)  
`helper.number_to_human_size(1024)`

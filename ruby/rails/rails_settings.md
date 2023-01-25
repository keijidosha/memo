# Rails: 設定

* lib ディレクトリ以下に配置した Ruby クラスをロードパスに追加し、Controller などから参照できるようにする  
  config/application.rb  
  ```
  class Application < Rails::Application
    # ...
    config.autoload_paths << Rails.root.join('lib')
  end
  ```
  ※lib ディレクトリ配下の Rubyソースを参照するクラス側で require しないこと  
  　require しなくても読み込まれる  
  　require すると、developmentモードで lib配下の Rubyソースを修正しても自動的リロードされないため。


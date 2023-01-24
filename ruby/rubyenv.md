# rubyenv

[Mac インストール]
```
brew update
brew upgrade
brew doctor
brew install rbenv ruby-build rbenv-gem-rehash
rbenv install -l
rbenv install 2.2.3
rbenv rehash
rbenv versions
rbenv global 2.2.3
rbenv version
ruby -v
```

vim ~/.bash_profile
```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

[SSL証明書]
```
ruby -ropenssl -e "p OpenSSL::X509::DEFAULT_CERT_FILE"
```
=> "/usr/local/etc/openssl/cert.pem"
```
sudo curl "https://curl.haxx.se/ca/cacert.pem" -o /usr/local/etc/openssl/cert.pem
```
(参考) http://qiita.com/emadurandal/items/a60886152a4c99ce1017

[特定のディレクトリ以下に gem をインストール]  
vi setenv.sh
```
PROJECT_HOME=/Users/hoge/project/ruby
export GEM_HOME=$PROJECT_HOME/gems/gems
export RUBYLIB=$PROJECT_HOME/gems/lib
export RUBY_USER_INSTALL=true
export PATH=$PATH:$PROJECT_HOME/gems/gems/bin
```

gem をインストール
```
. setenv.sh
# 設定内容を確認
gem environmet

gem install sqlite3
gem install mysql2
```

[もしOS側の ruby に切り替える場合は]
```
rbenv global system
```

# Ruby: smtp

## 準備

`gem install mail`

## 実装

```
require "mail"

mail = Mail.new
mail.from = "hoge@gmail.com"
mail.to = "fuga@gmail.com"
mail.subject = "test"
mail.charset = "UTF-8"
mail.body = "テストtestてすと"
mail.add_file "/tmp/1.png"
mail["User-Agent"] = "ruby mailer 1.0"

mail.delivery_method :smtp, {
  address: "smtp.gmail.com",
  port: 465,
  tls: true,
  openssl_verify_mode: OpenSSL::SSL::VERIFY_NONE,
  enable_starttls_auto: false,
  authentication: :login,
  domain: "gmail.com",
  user_name: "hoge@gmail.com",
  password: "password"
}

mail.deliver!
```

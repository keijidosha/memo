# Ruby: HTTP

## POST

* (例1)
  ```
  url = 'https://host/path'
  uri = URI.parse(url)
  http = Net::HTTP.new(uri.host, uri.port)
  http.use_ssl = uri.scheme == 'https'
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  http.start() { |h|
      req = Net::HTTP::Post.new(uri.path)
      # HTTPヘッダーセット
      req["headername"] = 'data'
      # 送信データセット
      req.set_form_data({ 'data1' => 'hoge', 'data2' => fuga })
      res = h.request(req)
      case res
      when Net::HTTPSuccess
          puts res.body
      when Net::HTTPRedirection
          puts res['location']
      else
          puts res.header.inspect + response.body
      end
  }
  ```
* (例2)
  ```
  require 'net/http'
  require 'net/https'
  Net::HTTP.version_1_2
  require 'uri'
  
  USE_SSL = true
  
  if USE_SSL then
      schema = "https"
  else
      schema = "http"
  end
  
  # URL を設定
  uri = URI.parse( schema + '://hostname/path' )
  http = Net::HTTP.new( uri.host, uri.port )
  # SSL の場合は、証明書を設定
  if USE_SSL then
      http.use_ssl = true
      http.verify_mode = OpenSSL::SSL::VERIFY_PEER  # or OpenSSL::SSL::VERIFY_NONE
      http.ca_file = "/opt/local/lib/ruby1.9/gems/1.9.1/gems/rubygems-update-1.3.5/test/public_cert.pem";
      http.verify_depth = 5
  end
  
  http.start {
      req = Net::HTTP::Post.new( uri.path )
      req.set_form_data( {
          :param1 => "hoge",
          :param2 => "fuga",
      } )
      res = http.request( req )
      puts res
  }
  ```

## WebDAV

* PUT
  ```
  input_file = "/Users/hoge/upload.txt"
  output_file = "/var/www/upload/hoge.txt"
  host = "hoge.com"
  port = 443
  baseic_auth_user = 'userid'
  baseic_auth_password = 'password'
  
  http = Net::HTTP.new(host, port, nil, nil)
  http.use_ssl = true
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  req = Net::HTTP::Put.new(output_file)
  req.basic_auth baseic_auth_user, baseic_auth_password
  req.content_length = File.size(input_file)
  File.open(input_file, 'rb'){ |io|
      req.body_stream = io
      res = http.request(req)
      raise StandardError.new("Invalid HttpResponse Code: #{res.code} #{res.message}") unless res.code == '201'
  }
  ```
* mkdir
  ```
  host = "hoge.com"
  port = 443
  new_dir = "/var/www/upload/new"
  baseic_auth_user = 'userid'
  baseic_auth_password = 'password'
  
  http = Net::HTTP.new(host, port, nil, nil)
  http.use_ssl = true
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  
  req = Net::HTTP::Mkcol.new(new_dir)
  req.basic_auth baseic_auth_user, baseic_auth_password
  res = http.request(req)
  raise StandardError.new("Invalid HttpResponse Code: #{res.code} #{res.message}") unless res.code == '201'
  ```
* DELETE
  ```
  host = "hoge.com"
  port = 443
  target_file = "/var/www/upload/hoge.txt"
  baseic_auth_user = 'userid'
  baseic_auth_password = 'password'
  
  http = Net::HTTP.new(host, port, nil, nil)
  http.use_ssl = true
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  req = Net::HTTP::Delete.new(target_file)
  req.basic_auth baseic_auth_user, baseic_auth_password
  res = http.request(req)
  raise StandardError.new("Invalid HttpResponse Code: #{res.code} #{res.message}") unless res.code == '204'
  ```
* COPY
  ```
  host = "hoge.com"
  port = 443
  source_file = "/var/www/upload/hoge.txt"
  dest_file = "/var/www/upload/fuga.txt"
  baseic_auth_user = 'userid'
  baseic_auth_password = 'password'
  
  http = Net::HTTP.new(host, port, nil, nil)
  http.use_ssl = true
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  req = Net::HTTP::Copy.new(source_file)
  req['Destination'] = dest_file
  req.basic_auth baseic_auth_user, baseic_auth_password
  res = http.request(req)
  raise StandardError.new("Invalid HttpResponse Code: #{res.code} #{res.message}") unless res.code == '204'
  ```
* MOVE
  ```
  host = "hoge.com"
  port = 443
  source_file = "/var/www/upload/hoge.txt"
  dest_file = "/var/www/upload/fuga.txt"
  baseic_auth_user = 'userid'
  baseic_auth_password = 'password'
  
  http = Net::HTTP.new(host, port, nil, nil)
  http.use_ssl = true
  http.verify_mode = OpenSSL::SSL::VERIFY_NONE
  req = Net::HTTP::Move.new(source_file)
  req['Destination'] = dest_file
  req.basic_auth baseic_auth_user, baseic_auth_password
  res = http.request(req)
  raise Exception.new("Invalid HttpResponse Code: #{res.code} #{res.message}") unless res.code == '204'
  ```


(参考) http://d.hatena.ne.jp/alunko/20071028/1193523622

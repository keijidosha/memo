# PHP

## 文字列

* Unicode文字チェック
  ```
  if( mb_check_encoding($str, "UTF-8")) {
    # OK
  } else {
    # NG
  }
  ```
* JIS(ISO-2022-JP)チェック
  ```
  if( mb_check_encoding($str, "ISO-2022-JP")) {
    # OK
  } else {
    # NG
  }
  ```

## 環境

* アップロードファイルの上限サイズを変更  
Apache 上で実行している場合、/etc/php5/apache2/php.ini に記述されている upload_max_filesize の値を変更して Apache を再起動。

## Tips

### SMTP認証付きでメール送信

1. PEARをインストール  
   * Mac  
     OSX El Capitan の場合、セキュリティ解除が必要  
       * PEARは /usr/local 以下を参照する
       * El Capitan では /usr/local は、sudo でも書き込めないため
     1. Command + R
     1. コンソールを開く  
        `csrutil disable`
     1. 再起動
        ```
        cd /etc/
        sudo cp php.ini.default php.ini
        sudo vim php.ini  
        `include_path = ".:/php/includes:/usr/lib/php/pear"`
        sudo php /usr/lib/php/install-pear-nozlib.phar
        sudo /usr/bin/pear channel-update pear.php.net
        sudo /usr/bin/pear upgrade-all
        sudo pear install Mail
        sudo pear install Mail_Mime
        sudo pear install Net_SMTP
        ```
     1. 再起動
     1. Command + R
     1. コンソールを開く  
        `csrutil enable`
   * Windows  
     d:\php フォルダにインストールする場合の例  
     ※PATH を d:\php に通しておく
     ```
     d:
     cd \php
     md pear
     cd pearr
     ```
     http://pear.php.net/go-pear.phar を d:\php\pear に保存
     ```
     php go-pear.phar
     cd \php\pear
     pear channel-update pear.php.net
     pear upgrade-all
     pear install Mail
     pear install Mail_Mime
     pear install Net_SMTP
     ```
     d:\php\php.ini を編集  
     次の行を追加、またはコメント解除
     ```
     include_path = ".;d:\php\pear\pear"
     extension_dir = "d:\php\ext"
     extension=php_mbstring.dll
     extension=php_openssl.dll
     ```

### 簡易サーバを立ち上げる

`php -S 0.0.0.0:8000`
起動したディレクトリがドキュメントルートになる。  
(例) 起動したディレクトリに test.php を置くと、http://localhost:8000/test.php でアクセスできる。


### 文字列

* 正規表現  
  preg_match( '/(\d+)_(\d+)/', '530-0001', $matches );  
  echo $matches[1], "\n", $matches[2], "\n";  


### ファイル

* ファイルを読み込む  
  $data = file_get_contents( "hoge.txt" );
* ファイルに書き込む  
  file_put_contents( "hoge.txt", $data, FILE_APPEND );

### HTTP

* POSTされた内容を取得  
`file_get_contents( "php://input" );`

### メール

* SMTP認証付きで送信
  ```
  require_once('Mail.php');
  require_once('Mail/mime.php');
  
  $from = "hoge@example.com";
  $to = "fuga@example.com";
  $title = "テストメールのサブジェクト";
  $body = <<<EOT
  テストメールの内容
  EOT;
  
  mb_language("japanese");
  mb_internal_encoding("utf8");
  $params =
      array(
          'host' => 'tls://smtp.example.com',
          'port' => 465,
          'auth' => true,
          'protocol' => 'SMTP_AUTH',
          'debug' => false,
          'username' => 'hoge@example.com',
          'password' => 'smtppassword',
      );
  $headers  =
      array (
          'To' => $to,
          'From' => $from,
          'Subject' => mb_encode_mimeheader($title),
      );
  
  $body = mb_convert_encoding($body, "ISO-2022-JP", "auto");
  $smtp = Mail::factory('smtp',  $params);
  $smtp->send($to,  $headers,  $body);
  ```

### パイプ、IOリダイレクト系

* 標準入力に渡されたデータを取得  
`$data = file_get_contents( "php://stdin" );`

### 日付

* 日付を文字列として取得  
  date_default_timezone_set("Asia/Tokyo");  
  echo date("Y-m-d_H-i-s");  
* 文字列を日付に変換  
  * strptime(Windowsは未サポート)  
    ```
    $d = strptime( "20160305103045", "%Y%m%d%H%M%S" );
    echo $d["tm_year"]+1900, "-",$d["tm_mon"]+1, "-", $d["tm_mday"], " ", $d["tm_hour"], ":", $d["tm_min"], ":", $d["tm_sec"],"\n";
    ```
    ==> "2016-3-5 10:30:45"
  * date_parse_from_format(Windows もサポート)  
    ```
    $d = date_parse_from_format( 'YmdHis', "20160305103045" );
    echo $d["year"], "-",$d["month"], "-", $d["day"], " ", $d["hour"], ":", $d["minute"], ":", $d["second"],"\n";
    ```
    ==> "2016-3-5 10:30:45"
  * DateTimeクラス  
    `$d = date_create("20160701103045");`  
    または  
    ```
    $d = new DateTime("20160305103045");
    echo $d->format("Y-m-d H:i:s")."\n"
    ```
    ==> "2016-03-05 10:30:45"
  * 書式を指定
    ```
    $d = DateTime::createFromFormat("mdYHis", "03052016103045");
    echo $d->format("Y-m-d H:i:s")."\n
    ```
    ==> "2016-03-05 10:30:45"
* DateTimeクラスを使って時間差を表示  
  ```
  $d1 = new DateTime("2016-05-01 10:00:00");
  $d2 = new DateTime("2016-05-01 10:30:00");
  $diff = $d2->diff( $d1 );
  echo $diff->format( '%H:%I:%S' ), "\n";
  ```
  ==> "00:30:00"

### JSON

標準入力から受け取った JSON を pretty print  
```bash
curl http://host/api/json | php -r 'echo json_encode(json_decode(fgets(STDIN)), JSON_PRETTY_PRINT);'
```


### 環境

* WEBから(HTTPリクエストで)起動されたか、コマンドラインから起動されたかを判定
  ```
  if( function_exists( "getallheaders" )) {
    // WEBから起動された場合は、受信データを取得
    $result_data = file_get_contents( "php://input" );
  } else {
    // コマンドラインから起動された場合は標準入力からデータを取得
    $result_data = file_get_contents( "php://stdin" );
  }
  ```
* コマンドラインからの OPCacheクリアサンプル
  ```
  php -r 'opcache_reset();'
  ```
  または
  ```
  #!/bin/bash
  WEBDIR=/var/www/html/
  RANDOM_NAME=$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)
  echo "<?php opcache_reset(); ?>" > ${WEBDIR}${RANDOM_NAME}.php
  curl http://localhost/${RANDOM_NAME}.php
  rm ${WEBDIR}${RANDOM_NAME}.php
  ```
  (引用) [http://phpweb.hostnet.com.br/manual/ja/function.opcache-reset.php](http://phpweb.hostnet.com.br/manual/ja/function.opcache-reset.php)
* CentOS6 に PHP 5.6 をインストール
  ```
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
  sudo rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
  sudo yum install --enablerepo=remi,remi-php56 php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt php-mysqlnd
  ```


### その他

* 構文チェック  
  `php -l hoge.php`



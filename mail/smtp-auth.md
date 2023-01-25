# smtp-auth

## perl で smtp-auth のパスワード生成

### DIGEST-MD5

* perl のソース
  ```
  #!/usr/bin/perl
   
  use Socket;   
  use MIME::Base64 ;
  use Digest::HMAC_MD5 qw(hmac_md5 hmac_md5_hex);
  use Digest::MD5  qw(md5 md5_hex md5_base64);
   
  # AUTH DIGEST-MD5
   
  $USER = $ARGV[0];
  $PASS = $ARGV[1];
  $HASH = $ARGV[2];
  $ticket = decode_base64($HASH);
  ($s = $ticket) =~ s/,/,\n    =>"/og ;
  print "$str\n    =>$s\n" ;
  my $retcode= &digest_md5($ticket) ;
  ($s = $retcode) =~ s/,/,\n    <=="/og ;
  my $ENPASS=encode_base64($retcode,"") ;
  print "\n    <<=$s";
  print "\n$ENPASS\n";
   
  sub digest_md5 {
      my ($ticket) =@_ ;
      my %ckey = map { /^([^=]+)="?(.+?)"?$/ } split(/,/, $ticket);
      my $realm  = $ckey{realm} ;
      my $nonce  = $ckey{nonce};
      my $cnonce = &make_cnode();
      my $qop = 'auth';
      my $nc  = '00000001';
      my $uri = "smtp/local/$realm" ;
      my($hv, $a1, $a2);
   
      $hv = md5("$USER:$realm:$PASS");
      $a1 = md5_hex("$hv:$nonce:$cnonce");
      $a2 = md5_hex("AUTHENTICATE:$uri");
      $hv = md5_hex("$a1:$nonce:$nc:$cnonce:$qop:$a2");
      return qq(username="$USER",realm="$realm",nonce="$nonce",nc=$nc,cnonce="$cnonce",digest-uri="$uri",response=$hv,qop=$qop);
  }
   
  sub make_cnode {
      my $len  = 16 ;
      my $i ;
      my $s = '' ;
      for($i=0;$i<$len;$i++) { $s .=chr(rand(256)) ; }
      $s = encode_base64($s, "");
      $s =~ s/\W/X/go;
      substr($s, 0, $len);
  }
  ```

#### 使い方

1. SMTP サーバに接続後、次のコマンドを入力。  
`AUTH DIGEST-MD5`  
1. perl digest-md5.pl <ユーザID> <パスワード> <サーバから返されたBASE64文字列>
1. 表示された文字列をサーバに送信

### CRAM-MD5

* perl のソース
  ```
  #!/usr/bin/perl
   
  use Socket;   
  use MIME::Base64 ;
  use Digest::HMAC_MD5 qw(hmac_md5 hmac_md5_hex);
  use Digest::MD5  qw(md5 md5_hex md5_base64);
   
  # AUTH CRAM-MD5
   
  $USER = $ARGV[0];
  $PASS = $ARGV[1];
  $HASH = $ARGV[2];
   
  $str =~ s/\r\n$//og ; ;
  $ticket = decode_base64($HASH)  ;
  print "$str\n    ==>" , $ticket , "\n" ;
   
  $encPass = hmac_md5_hex($ticket, $PASS);
  my $ENPASS=encode_base64("$USER $encPass","");
   
  print "\n$ENPASS\n";
  ```
#### 使い方

1. SMTP サーバに接続後、次のコマンドを入力。  
`AUTH CRAM-MD5`  
1. perl cram-md5.pl <ユーザID> <パスワード> <サーバから返されたBASE64文字列>
1. 表示された文字列をサーバに送信

参考サイト  
http://www.aritia.org/hizumi/dsl/page_04.htm

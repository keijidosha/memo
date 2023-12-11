* [リネーム](#リネーム)
  * [java.io.File#renameTo](#javaiofilerenameto)
  * [java.nio.file.Files#move](#javaniofilefilesmove)
* [ファイルロックを使ったプロセス間排他制御](#ファイルロックを使ったプロセス間排他制御)
  * [ブロックあり](#ブロックあり)
  * [ブロックなし](#ブロックなし)
  * [ブロックをかけてから文字列をファイルに追記する](#ブロックをかけてから文字列をファイルに追記する)
* [その他](#その他)
  * [テキストファイル読み込みでエンコーディングが違っていても例外がスローされない読み込み方](#テキストファイル読み込みでエンコーディングが違っていても例外がスローされない読み込み方)
    * [InputStreamReader を使う](#inputstreamreader-を使う)
    * [同じことを FileChannel + CharsetDecoder でやる](#同じことを-filechannel--charsetdecoder-でやる)
* [トラブルシューティング](#トラブルシューティング)
  * [Linux環境でSambaでマウントしたディレクトリに対して書き込んだファイルをリネームしても実際にはリネームされない現象が発生。](#linux環境でsambaでマウントしたディレクトリに対して書き込んだファイルをリネームしても実際にはリネームされない現象が発生)

## リネーム
### java.io.File#renameTo

* Mac  
  * リネーム先ファイルが存在する場合もリネームできる
  * リネームより先にファイルがオープンされている場合、オープンしているプロセスにはリネーム前のファイルだけが見えている
  * リネーム後にオープンされると、リネーム後のファイルが見えるようにする
* Windows  
  * リネーム先のファイルが存在する場合、リネームはされない。(エラーも発生しない)

### java.nio.file.Files#move

* Mac
  * StandardCopyOption.REPLACE_EXISTING を指定すると、リネーム先ファイルが存在する場合もリネームできる
  * リネームより先にファイルがオープンされている場合、オープンしているプロセスにはリネーム前のファイルだけが見えている
  * リネーム後にオープンされると、リネーム後のファイルが見えるようにする
* Windows
  * StandardCopyOption.REPLACE_EXISTING を指定すると、リネーム先ファイルが存在する場合もリネームできる
  * リネームより先にファイルがオープンされている場合、次のエラーが発生する  
    java.nio.file.FileSystemException: プロセスはファイルにアクセスできません。別のプロセスが使用中です。

##   ファイルロックを使ったプロセス間排他制御

スレッド間の排他制御には使えません(OverlappingFileLockException がスローされる)

### ブロックあり

```
String FILE_NAME = "lockfile.lck";
try ( FileChannel fc = FileChannel.open( Paths.get( FILE_NAME ),
        StandardOpenOption.CREATE, StandardOpenOption.WRITE )) {
  FileLock lock = fc.lock();
  if( lock != null ) {
    try {
      System.out.println( "Get lock !" );
      for( int ii=1; ii<=7; ii++ ) {
        try {
          Thread.sleep( 1000 );
          System.out.print( ii );
        } catch( InterruptedException ex ) {}
      }
      System.out.println( "" );
    } finally {
      System.out.println( "Lock release" );
      lock.release();
    }
  } else {
    System.out.println( "Can't get lock ..." );
  }
}
```

### ブロックなし

```
String FILE_NAME = "lockfile.lck";
try ( FileChannel fc = FileChannel.open( Paths.get( FILE_NAME ),
        StandardOpenOption.CREATE, StandardOpenOption.WRITE )) {
  FileLock lock = fc.tryLock();
  if( lock != null ) {
    try {
      System.out.println( "Get lock !" );
      for( int ii=1; ii<=7; ii++ ) {
        try {
          Thread.sleep( 1000 );
          System.out.print( ii );
        } catch( InterruptedException ex ) {}
      }
      System.out.println( "" );
    } finally {
      System.out.println( "Lock release" );
      lock.release();
    }
  } else {
    System.out.println( "Can't get lock ..." );
  }
}
```

### ブロックをかけてから文字列をファイルに追記する

```
String FILE_NAME = "lockfile.lck";
try ( FileChannel fc = FileChannel.open( Paths.get( FILE_NAME ),
        StandardOpenOption.CREATE, StandardOpenOption.WRITE, StandardOpenOption.APPEND )) {
    FileLock lock = fc.lock();
    if( lock != null ) {
        try {
            System.out.println( "Get lock !" );
            val cs = Charset.forName("UTF-8");
            fc.write(cs.encode("hoge"));
            fc.write(cs.encode("\r\n"));
        } catch(Exception ex ) {
            ex.printStackTrace();
        } finally {
            System.out.println( "Lock release" );
            lock.release();
        }
    } else {
        System.out.println( "Can't get lock ..." );
    }
}
```

## その他
### テキストファイル読み込みでエンコーディングが違っていても例外がスローされない読み込み方
#### InputStreamReader を使う

```
BufferedReader reader = BufferedReader( InputStreamReader( FileInputStream( "sjis.txt" ), Charsets.UTF_8 ));
String line = null;
while(( line = reader.readLine()) != null ) {
    System.out.println( line );
}
```

#### 同じことを FileChannel + CharsetDecoder でやる

```
ByteBuffer bbuf = ByteBuffer.allocateDirect( 4096 );
CharBuffer cbuf = CharBuffer.allocate( 4096 )
CharsetDecoder decoder = StandardCharsets.UTF_8.newDecoder();
decoder.onMalformedInput( CodingErrorAction.REPLACE );
FileChannel fc = FileChannel.open( Paths.get( "sjis.txt" ), StandardOpenOption.READ );
try {
    int len;
    while(( len = fc.read( bbuf )) >= 0 ) {
        bbuf.flip();
       CoderResult rs = decoder.decode( bbuf, cbuf, false )
          // CodingErrorAction.REPLACE を指定していると、実際にはスローされない
       if( rs.isMalformed() || rs.isUnmappable()) rs.throwException()
       cbuf.flip();
          System.out.print( cbuf );
       bbuf.compact();
    }
    fc.close();
} finally {
    fc.close();
}
```

## トラブルシューティング
### Linux環境でSambaでマウントしたディレクトリに対して書き込んだファイルをリネームしても実際にはリネームされない現象が発生。

次の環境で発生
* CentOS 6.6
* Java 1.8.0_152

java.nio.file.Files.move(Path source, Path target, CopyOption... options) を使ってリネームする時、options に次のどちらか、または両方を指定していると発生。  
(Sambaサーバ側のログに rename コマンドの実行履歴は出力されていない。  
つまりサーバでは rename されたと認識していない模様。。)  
* StandardCopyOption.ATOMIC_MOVE
* StandardCopyOption.REPLACE_EXISTING

どちらも指定せず実行すると、確実にリネームされた。

※共有フォルダを別の Mac でもマウントし、出力されたファイルをエディタで開いて閉じた後に、同名のファイルにリネームしようとすると、発生しやすい。別の Mac でマウントしなかったら発生しない。

(回避策)  
options 付きでリネームしても、リネーム元のファイルが残っている場合は、options なしでリネームする。

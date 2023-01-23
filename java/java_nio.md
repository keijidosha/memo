# nio

## FileChannel

### FileChannelを使って文字列をファイルに書き込む

```
FileChannel fc = FileChannel.open(
    Paths.get( "hoge.txt" ),
    StandardOpenOption.WRITE,
    StandardOpenOption.CREATE,
    StandardOpenOption.TRUNCATE_EXISTING );

try {
    fc.write( StandardCharsets.UTF_8.encode( "ほげ\r\nふが\r\n" ));
} finally {
    fc.close();
}
```

### FileChannelを使って文字列をファイルから読み込む(1)

```
ByteBuffer bbuf = ByteBuffer.allocateDirect( 64 );
CharBuffer cbuf = CharBuffer.allocate( 64 );
CharsetDecoder decoder = StandardCharsets.UTF_8.newDecoder();
decoder.onMalformedInput( CodingErrorAction.IGNORE );
decoder.onUnmappableCharacter( CodingErrorAction.IGNORE );
FileChannel fc = FileChannel.open( Paths.get( "hoge.txt" ), StandardOpenOption.READ );
try {
    int len = 0;
    while(( len =fc.read( bbuf )) >= 0 ) {
        bbuf.flip();
        CoderResult rs = decoder.decode( bbuf, cbuf, false );
        // CodingErrorAction.IGNORE を指定していると、実際にはスローされない
       if( rs.isMalformed() || rs.isUnmappable() || rs.isOverflow() || rs.isUnderflow()) rs.throwException()
        cbuf.flip();
        System.out.print( cbuf );

        bbuf.compact();
    }
} finally {
    fc.close();
}
```

### FileChannelを使って文字列をファイルから読み込む(2)

```
ByteBuffer bbuf = ByteBuffer.allocateDirect( 64 );
CharBuffer cbuf = CharBuffer.allocate( 64 );
char[] charArray = cbuf.array();
CharsetDecoder decoder = StandardCharsets.UTF_8.newDecoder();
decoder.onMalformedInput( CodingErrorAction.IGNORE );
decoder.onUnmappableCharacter( CodingErrorAction.IGNORE );
StringBuilder sb = StringBuilder();
FileChannel fc = FileChannel.open( Paths.get( "hoge.txt" ), StandardOpenOption.READ );
try {
    int len = 0;
    while(( len =fc.read( bbuf )) >= 0 ) {
        bbuf.flip();
        CoderResult rs = decoder.decode( bbuf, cbuf, false );
        if( rs.isMalformed() || rs.isUnmappable()) rs.throwException()
           cbuf.flip();
        int pos = cbuf.position();
        int remain = cbuf.remaining();
        cbuf.get( charArray, pos, remain );
        sb.append( cbuf, cbuf.position(), cbuf.remaining());

        bbuf.compact();
    }

    System.out.println( sb );
} finally {
    fc.close();
}
```

(参考) http://www.java2s.com/Code/Java/File-Input-Output/ReadbytesfromthespecifiedchanneldecodethemusingthespecifiedCharsetandwritetheresultingcharacterstothespecifiedwriter.htm


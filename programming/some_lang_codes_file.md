# コード比較 : ファイル

Table of Contents
=================
* [読み込み](#読み込み)
   * [全読み込み](#全読み込み)
      * [PHP](#php)
      * [Ruby](#ruby)
      * [Java](#java)
      * [Kotlin](#kotlin)
      * [Haskell](#haskell)
      * [Elixir](#elixir)
      * [Go](#go)
   * [行読み込み](#行読み込み)
      * [PHP](#php-1)
      * [Ruby](#ruby-1)
      * [Java](#java-1)
      * [Kotlin](#kotlin-1)
      * [Elixir](#elixir-1)
      * [Go](#go-1)
   * [一定量(文字数/バイト)ずつ読み込み](#一定量文字数バイトずつ読み込み)
      * [go](#go-2)
   * [ファイル属性](#ファイル属性)
      * [ファイルのタイムスタンプを取得して YYYY/MM/DD HH:MM:SS 形式で表示する](#ファイルのタイムスタンプを取得して-yyyymmdd-hhmmss-形式で表示する)
         * [PHP](#php-2)
         * [Ruby](#ruby-2)
         * [Java](#java-2)
         * [Kotlin](#kotlin-2)
      * [今日の年、月、日のディレクトリが存在しない場合は作成して、スターバックスのロゴをダウンロード・保存。](#今日の年月日のディレクトリが存在しない場合は作成してスターバックスのロゴをダウンロード保存)
         * [PHP](#php-3)
            * [Ruby](#ruby-3)
         * [Java](#java-3)
            * [Kotlin](#kotlin-3)
* [バイト読み込み](#バイト読み込み)
   * [4バイト読み込んで、文字列、数値(LittleEndian, BigEndian)に変換](#4バイト読み込んで文字列数値littleendian-bigendianに変換)
      * [Kotlin](#kotlin-4)
      * [Go](#go-3)
* [バイト出力](#バイト出力)
   * [数値(LittleEndian, BigEndian)を 4バイトでファイルに出力](#数値littleendian-bigendianを-4バイトでファイルに出力)
      * [Go](#go-4)

## 読み込み

### 全読み込み

#### PHP
```php
<?php
echo file_get_contents( "read.txt" );
?>
```

#### Ruby
```ruby
puts File.open( "read.txt" ).read
```

#### Java
```java
import java.io.*;

public class ReadAll {
    public ReadAll() {
        try( Reader reader = new FileReader( "read.txt" )) {
            char[] buf = new char[4096];
            int len;
            StringBuilder sb = new StringBuilder(4096);
            while(( len = reader.read( buf )) >= 0 ) {
                sb.append( buf, 0, len );
            }
            System.out.println( sb );
        } catch( IOException ex ) {
            ex.printStackTrace();
        }
    }

    public static void main( String[] args ) {
        new ReadAll();
    }
}
```

#### Kotlin
```kotlin
import java.io.*

fun main( args: Array<String> ) {
    println( File( "read.txt" ).readText())
}
```

#### Haskell
```haskell
main = do
    text <- readFile "read.txt"
    putStrLn text
```

#### Elixir
```elixir
{ :ok, text } = File.read "read.txt"
IO.puts text
```
または
```elixir
case File.read "read.txt" do
{ :ok, text } -> IO.puts text
end
```

#### Go
```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {
    data, err := ioutil.ReadFile( "read.txt" )
    if err != nil {
        panic( err )
    }
    fmt.Print( string( data ))
}
```
***

### 行読み込み

#### PHP
```php
<?php
$file = fopen( "read.txt", "r" );
if( $file ) {
    while( $line = fgets( $file )) {
        echo $line;
    }
}
fclose( $file );
?>
```
読み込んだ 1行の行末に改行文字が付く

#### Ruby
```ruby
open( "read.txt" ) { |file|
    while line = file.gets
        print line
    end
}
```
読み込んだ 1行の行末に改行文字が付く

#### Java
```java
import java.io.*;

public class ReadLine {
    public ReadLine() {
        try( BufferedReader reader = new BufferedReader( new FileReader( "read.txt" ))) {
            String line = null;
            while(( line = reader.readLine()) != null ) {
                System.out.println( line );
            }
        } catch( IOException ex ) {
            ex.printStackTrace();
        }
    }

    public static void main( String[] args ) {
        new ReadLine();
    }
}
```
読み込んだ 1行の行末に改行文字が付かない

#### Kotlin
readLines() ==> すべて読み込んでから処理に入る(ファイルがあまりに大きいと OutOfMemory が発生する)
```kotlin
import java.io.*

fun main( args: Array<String> ) {
    File( "read.txt" ).readLines().forEach {
        println( it )
    }
}
```
useLines() ==> 1行ずつ読み込んで処理する
```kotlin
import java.io.*

fun main( args: Array<String> ) {
    File( "read.txt" ).useLines() {
        it.forEach {
            println( it )
        }
    }
}
```
読み込んだ 1行の行末に改行文字が付かない

#### Elixir
```elixir
defmodule ReadLine do
    def readline( file ) do
        case IO.read file, :line do
        :eof -> nil
        data ->
            IO.write data
            readline( file )
        end
    end
end

{ :ok, file } = File.open( "read.txt", [:read ])
try do
    ReadLine.readline( file )
after
    File.close( file )
end
```
読み込んだ 1行の行末に改行文字が付く

#### Go
```go
package main

import (
    "fmt"
    "os"
    "bufio"
    "log"
)

func main() {
    fp, err := os.Open( "ReadLine.go" )
    if err != nil {
        log.Fatal( err )
    }

    defer fp.Close()

    scanner := bufio.NewScanner( fp )
    for scanner.Scan() {
        fmt.Println( scanner.Text())
    }

    if err = scanner.Err(); err != nil {
        log.Fatal( err )
    }
}
```
読み込んだ 1行の行末に改行文字は付かない

scanner のバッファサイズはデフオルト 64KB なので注意。  
https://mickey24.hatenablog.com/entry/bufio_scanner_line_length

次のようにしてもよい(行末に改行は付かない)  
```go
  package main
  
  import (
      "fmt"
      "os"
      "io"
      "bufio"
      "log"
  )
  
  func main() {
      fp, err := os.Open( "ReadLine.go" )
      if err != nil {
          log.Fatal( err )
      }
  
      defer fp.Close()
  
      reader := bufio.NewReaderSize( fp, 8192 )
      for {
          line, _, err := reader.ReadLine()
          if err == io.EOF {
              break
          } else if err != nil {
              log.Fatal(err)
          }
          fmt.Println(string(line))
      }
  }
```

### 一定量(文字数/バイト)ずつ読み込み
#### go
```golang
  package main
  
  import(
      "fmt"
      "io"
      "bufio"
      "os"
      "log"
  )
  
  func useReadBytes() {
      file, err := os.Open( "ReadLine.go" )
      if err != nil {
          log.Fatal( err )
      }
      defer file.Close()
  
      buf := make( []byte, 64 )
      reader := bufio.NewReader( file )
  
      for {
          size, err := reader.Read( buf )
          if err != nil {
              if err == io.EOF {
                  break
              } else {
                  log.Fatal( err )
              }
          }
          fmt.Print( string(buf[0:size]))
      }
  }
  
  func main() {
  
      useReadBytes()
  }
```
***

### ファイル属性

#### ファイルのタイムスタンプを取得して YYYY/MM/DD HH:MM:SS 形式で表示する

##### PHP
```php
<?php
date_default_timezone_set( "Asia/Tokyo" );
echo date( "Y/m/d H:i:s\n", filemtime( "timestamp.php" ));
?>
```

##### Ruby
```ruby
puts File::mtime( "timestamp.rb" ).strftime( "%Y/%m/%d %H:%M:%S" )
```

##### Java
```java
import java.io.*;
import java.text.*;
import java.util.*;

public class Timestamp {
    public static void main( String args[] ) {
        System.out.println( new SimpleDateFormat( "yyyy/MM/dd HH:mm:ss" ).format(
                new Date( new File( "Timestamp.java" ).lastModified())));
    }
}
```

##### Kotlin
```kotlin
import java.io.*
import java.text.*
import java.util.*

fun main( args: Array<String> ) {
    println( SimpleDateFormat( "yyyy/MM/dd HH:mm:ss" ).format(
        Date( File( "Timestamp.kt" ).lastModified())))
}
```
または
```kotlin
import java.io.*
import java.text.*
import java.util.*

fun main( args: Array<String> ) {
    "Timestamp.kt".toFile().lastModified().toDate().format( "yyyy/MM/dd HH:mm:ss" ).println()
}

fun String.toFile() = File( this )
fun Long.toDate() = Date( this )
fun Date.format( fmt: String ) = SimpleDateFormat( fmt ).format( this )
fun String.println() = println( this )
```
***

#### 今日の年、月、日のディレクトリが存在しない場合は作成して、スターバックスのロゴをダウンロード・保存。

##### PHP
```php
<?php
date_default_timezone_set( "Asia/Tokyo" );

$year = date( "Y" );
$month = date( "m" );
$day = date( "d" );

if( ! file_exists( $year )) {
    mkdir( $year );
}
if( ! file_exists( "$year/$month" )) {
    mkdir( "$year/$month" );
}
if( ! file_exists( "$year/$month/$day" )) {
    mkdir( "$year/$month/$day" );
}

# その1(全データを受信してからファイルに書き込む)
$file = fopen( "$year/$month/$day/logo.png", "w" );
fwrite( $file, file_get_contents( "http://www.starbucks.co.jp/images/og/logo.png" ));
fclose( $file );

# その2(4096バイトずつ読み書きする)
$file = fopen( "$year/$month/$day/logo.png", "w" );
$http = fopen( "http://www.starbucks.co.jp/images/og/logo.png", "r" );
while( !feof( $http )) {
    $data = fread( $http, 4096 );
    fwrite( $file, $data );
}
fclose( $http );
fclose( $file );

?>
```

###### Ruby
```ruby
require "net/http"

# host = "www.starbucks.co.jp"
# path = "/images/og/logo.png"
# ローカルテスト用URL
host = "localhost"
port = 8000
path = "/logo.png"
url = "http://#{host}:#{port}#{path}"

now = Time.now
year = now.strftime( "%Y" )
month = now.strftime( "%m" )
day = now.strftime( "%d" )

if ! Dir.exist?( year )
    Dir.mkdir( year )
end
if ! Dir.exist?( "#{year}/#{month}" )
    Dir.mkdir( "#{year}/#{month}" )
end
if ! Dir.exist?( "#{year}/#{month}/#{day}" )
    Dir.mkdir( "#{year}/#{month}/#{day}" )
end

# その1
File.write( "#{year}/#{month}/#{day}/logo.png",
    Net::HTTP.get( URI.parse( url )) )

# その2
require 'open-uri'
open( "#{year}/#{month}/#{day}/logo.png", "wb" ){ |file|
    open( url, "rb" ){ |http|
      file.write( http.read )
    }
}

# その2.5(1024バイトずつ読み書きする)
open( "#{year}/#{month}/#{day}/logo.png", "wb" ){ |file|
    open( url, "rb" ){ |http|
        while ! http.eof
            file.write( http.read( 1024 ))
        end
    }
}

# その3(少しずつ読み書きする)
open( "#{year}/#{month}/#{day}/logo.png", "wb" ){ |file|
    Net::HTTP.start( host, port ) do |http|
        http.request_get( path ) { |res|
            res.read_body do |segment|
                file.write( segment )
            end
        }
    end
}
```

##### Java
```java
import java.io.*;
import java.net.*;
import java.text.*;
import java.util.*;

public class DlToDir {
    public static void main( String[] args ) {
        // String url = "http://www.starbucks.co.jp/images/og/logo.png";
        // テスト用 URL
        String url = "http://localhost:8000/logo.png";
        DateFormat df = new SimpleDateFormat( "yyyyMMdd" );
        String today = df.format( new Date());
        String year = today.substring( 0, 4 );
        String month = today.substring( 4, 6 );
        String day = today.substring( 6 );

        new File( String.format( "%s/%s/%s", year, month, day )).mkdirs();

        try( OutputStream file = new FileOutputStream( String.format( "%s/%s/%s/logo.png", year, month, day ));
            InputStream http = new URL( url ).openConnection().getInputStream()) {
            byte[] buf = new byte[4096];
            int len;
            while(( len = http.read( buf )) >= 0 ) {
                file.write( buf, 0, len );
            }
        } catch( MalformedURLException ex ) {
            ex.printStackTrace();
        } catch( IOException ex ) {
            ex.printStackTrace();
        }
    }
}
```

###### Kotlin
```kotlin
import java.io.*
import java.net.*
import java.text.*
import java.util.*

fun main( args: Array<String> ) {
    //val url = "http://www.starbucks.co.jp/images/og/logo.png"
    val url = "http://localhost:8000/logo.png"

    val df = SimpleDateFormat( "yyyy/MM/dd" )
    val today = df.format( Date())

    File( today ).mkdirs()

    FileOutputStream( "$today/logo.png" ).use() { file ->
        URL( url ).openConnection().getInputStream().use() { http ->
            http.copyTo( file )
        }
    }
}
```
InputStream.copyTo() で参照される DEFAULT_BUFFER_SIZE の定数値は 8192

## バイト読み込み
### 4バイト読み込んで、文字列、数値(LittleEndian, BigEndian)に変換
#### Kotlin
```kotlin
import java.io.RandomAccessFile
import java.nio.charset.StandardCharsets

// バイト配列を LittleEndian に変換
fun toUnsignedLongWithLittleEndian( buf: ByteArray, start: Int, length: Int ): Long {
    var num  = 0L
    var keta = 0
    for( idx in start..( start + length - 1 )) {
        num = num or (( buf[idx].toLong() and 255 ) shl ( keta * 8 ))
        keta++
    }

    return num
}

// バイト配列を BigEndian に変換
fun toUnsignedLongWithBigEndian( buf: ByteArray, start: Int, length: Int ): Long {
    var num = 0L
    for( idx in start..( start + length - 1 )) {
        num = num shl 8
        num = num or ( buf[idx].toLong() and 255 )
    }

    return num
}

const val fileName = "hoge.wav"

fun main( args: Array<String>) {
    val buf = ByteArray( 4 )

    RandomAccessFile( fileName, "r" ).use { file ->
        file.read( buf, 0, 4 )
        println( buf.toString( StandardCharsets.UTF_8 ))

        file.read( buf, 0, 4 )
        println( toUnsignedLongWithLittleEndian( buf, 0, 4 ).toString( 16 ))
        println( toUnsignedLongWithBigEndian( buf, 0, 4 ).toString( 16 ))
    }
}
```

#### Go
```golang
package main

import (
    "fmt"
    "os"
    "log"
)

// バイト配列(スライス)を LittleEndian で符号なし整数に変換
func bytesToUIntLittleEndian( buf []byte, start int, len int ) uint {
    var sum uint = 0
    max := start + len
    var pos uint = 0
    for idx := start; idx < max; idx++ {
        sum += uint( buf[idx] ) << (pos * 8)
        pos++
    }
    return sum
}

// バイト配列(スライス)を BigEndian で符号なし整数に変換
func bytesToUIntBigEndian( buf []byte, start int, len int ) uint {
    var sum uint = 0
    max := start + len
    var pos uint = 0
    for idx := max-1; idx >= 0; idx-- {
        sum += uint( buf[idx] ) << (pos * 8)
        pos++
    }
    return sum
}

func main() {
    const fileName = "hoge.wav"

    file, err := os.Open( fileName )
    if err != nil {
        log.Fatal( err )
    }

    defer file.Close()

    buf := make( []byte, 4 )
    file.Read( buf )
    fmt.Println( string( buf ))

    file.Read( buf )
    // LittleEndian に変換
    fmt.Printf("%x\n", bytesToUIntLittleEndian( buf, 0, 4 ))
    // BigEndian に変換
    fmt.Printf("%x\n", bytesToUIntBigEndian( buf, 0, 4 ))
}
```

## バイト出力
### 数値(LittleEndian, BigEndian)を 4バイトでファイルに出力
#### Go
```go
package main

import (
    "log"
    "os"
)

// 符号なし整数を LittleEndian でバイト配列(スライス)に変換
func uintToBytesLittleEndian(intVal uint, len int8) []byte {
    buf := make([]byte, len)
    var ii int8
    remain := intVal
    for ii = 0; ii < len; ii++ {
        buf[ii] = byte(remain & 0xff)
        remain >>= 8
    }

    return buf
}

// 符号なし整数を BigEndian でバイト配列(スライス)に変換
func uintToBytesBigEndian(intVal uint, len int8) []byte {
    buf := make([]byte, len)
    var ii int8
    remain := intVal
    for ii = 0; ii < len; ii++ {
        buf[len-ii-1] = byte(remain & 0xff)
        remain >>= 8
    }

    return buf
}

func main() {
    const fileName = "hoge.bin"

    file, err := os.OpenFile(fileName, os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0664)
    if err != nil {
        log.Fatal(err)
    }

    defer file.Close()

    buf := make([]byte, 4)

    file.Read(buf)
    // LittleEndian で出力 => 4361bc00
    file.Write(uintToBytesLittleEndian(12345678, 4))
    // BigEndian で出力 => 00bc614e
    file.Write(uintToBytesBigEndian(12345678, 4))
}
```

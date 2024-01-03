# Java・Kotlin: 日付・時刻

* 現在日付・時刻を取得する  
  ```
  import java.time.LocalDateTime;
  
  LocalDateTime now = LocalDateTime.now();
  ```
* 現在日付・時刻を取得し、フォーマットして表示する
  ```
  import java.time.LocalDateTime;
  import java.time.format.DateTimeFormatter;
  
  DateTimeFormatter fmt = DateTimeFormatter.ofPattern( "yyyy/MM/dd HH:mm:ss.SSS" );
  LocalDateTime now = LocalDateTime.now();
  System.out.println( now.format( fmt ) );
  ```

* ファイルの最終更新日付を文字列化(書式化)する(Instant を LocalDateFormat に変換する)  
  ```
  import java.nio.file.Paths;
  import java.nio.file.Files;
  import java.time.LocalDateTime;
  import java.time.ZoneId;
  import java.time.format.DateTimeFormatter;
  
  Path path = Paths.get( "hoge.txt" );
  FileTime time = Files.getLastModifiedTime( path );
  LocalDateTime ldt = LocalDateTime.ofInstant( time.toInstant(), ZoneId.systemDefault());

  DateTimeFormatter df = DateTimeFormatter.ofPattern( "yyyy/MM/dd HH:mm:ss" );
  System.out.println( ldt.format( df ));
  ```
* RFC1123形式の日付・時刻を解析して日本時間で表示する  
  ```
  import java.time.*;
  import java.time.format.*;
  
  〜
  
  String dtStr = "Tue, 28 Feb 2017 08:30:00 GMT";
  ZonedDateTime zdt = ZonedDateTime.parse( dtStr, DateTimeFormatter.RFC_1123_DATE_TIME );
  ZonedDateTime jst = zdt.withZoneSameInstant( ZoneId.of( "Asia/Tokyo" ));
  String jstStr = jst.format( DateTimeFormatter.ofPattern( "yyyy/MM/dd HH:mm:ss" ));
  System.out.println( jstStr );
  ZonedDateTime#withZoneSameInstant() で、指定されたタイムゾーンの時間に変換される。
  "Asia/Tokyo" は "+0900" でもよい
  ZoneId.systemDefault() を使うと、Java を実行しているマシンに設定されているタイムゾーンが適用される模様
  
  ZonedDateTime から java.util.Date オブジェクトの変換は、ZonedDateTime#toInstant() と Date#from() を使う。
  Date dt = Date.from( jst.toInstant());
  ```

* java.util.Date から java.time.LocalDateTime に変換する  
  ```
  import java.util.Date;
  import java.time.LocalDateTime;
  import java.time.ZoneId;
  import java.time.format.DateTimeFormatter;
  
  Date now = new Date();
  LocalDateTime now2 = LocalDateTime.ofInstant( now.toInstant(), ZoneId.systemDefault());
  System.out.println( now2.format( DateTimeFormatter.ofPattern( "yyyy/MM/dd HH:mm:ss.SSS")));
  ```

* java.time.LocalDateTime から java.util.Date に変換する  
  ```
  import java.util.Date;
  import java.time.LocalDateTime;
  import java.time.ZoneOffset;
  import java.text.SimpleDateFormat;
  
  LocalDateTime now = LocalDateTime.now();
  Date now2 = Date.from( now.toInstant( ZoneOffset.of( "+0900" )));
  System.out.println( new SimpleDateFormat( "yyyy/MM/dd HH:mm:ss.SSS" ).format( now2 ));
  ```

* java.time.ZonedDateTime から java.util.Date に変換する  
  ```
  import java.util.Date;
  import java.time.ZonedDateTime;
  import java.text.SimpleDateFormat;
  
  ZonedDateTime now = ZonedDateTime.now();
  Date now2 = Date.from( now.toInstant());
  System.out.println( new SimpleDateFormat( "yyyy/MM/dd HH:mm:ss.SSS" ).format( now2 ));
  ```

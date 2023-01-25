# 日付・時刻

# 日付・時刻文字列をパースする

### PHP

```
<?php
date_default_timezone_set( "Asia/Tokyo" );

# その1(1970/1/1 00:00;00 からの経過秒数が返る)
$dt = strtotime( "2016/03/09 09:05:07" );
echo strftime( "%Y/%m/%d %H:%M:%S\n", $dt );

# その2(array が返る)
$dt = date_parse( "2016/03/09 09:05:07" );
echo sprintf( "%04d/%02d/%02d %02d:%02d:%02d\n",
  $dt["year"], $dt["month"], $dt["day"], $dt["hour"], $dt["minute"], $dt["second"] );

# その3(array が返る)
$dt = date_parse_from_format( "Y/m/d H:i:s", "2016/03/09 09:05:07" );
echo sprintf( "%04d/%02d/%02d %02d:%02d:%02d\n",
  $dt["year"], $dt["month"], $dt["day"], $dt["hour"], $dt["minute"], $dt["second"] );

# その4(array が返る, Windows環境では実装されていない)
$dt = strptime( "2016/03/09 09:05:07", "%Y/%m/%d %H:%M:%S" );
echo sprintf( "%04d/%02d/%02d %02d:%02d:%02d\n",
  $dt['tm_year'] + 1900, $dt['tm_mon'] + 1, $dt['tm_mday'],
  $dt['tm_hour'], $dt['tm_min'], $dt['tm_sec'] );
?>
```

### Ruby

```
# その1(Time型が返る)
require "time"
puts Time.strptime( "2016/03/09 09:05:07", "%Y/%m/%d %H:%M:%S" )

# その2(DateTime型が返る)
require "date"
puts DateTime.parse( "2016/03/09 09:05:07" )
```

### Java

```
import java.text.*;
import java.util.*;

public class Parse {
  public static void main( String[] args ) {
    DateFormat df = new SimpleDateFormat( "yyyy/MM/dd HH:mm:ss" );
    try {
      Date date = df.parse( "2016/03/09 09:05:07" );
      System.out.println( date );
    } catch( ParseException ex ) {
      ex.printStackTrace();
    }
  }
}
```

### Kotlin

```
import java.text.*

fun main( args: Array<String> ) {
  println( SimpleDateFormat( "yyyy/MM/dd HH:mm:ss" ).parse( "2016/03/09 09:05:07" ))
}
```



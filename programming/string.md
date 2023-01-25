# 文字列

## 文字列結合

### Java

```
String str1 = "abc";
String str2 = "123";
String str = str1 + str2;
```

```
StringBuilder sb = new StringBuilder(6);
sb.append( str1 ).append( str2 );
```


## 部分文字列取得

### Java

```
String str = "1234567890";

// 後方文字列
String substr = str.substring(3);
System.out.println(substr); // "4567890"

// 前方文字列
substr = str.substring(0, 3);
System.out.println(substr); // "123"

// 中間文字列
substr = str.substring(3, 6);
System.out.println(substr); // "456"
```

### Objective-C

```
NSString* str = @"1234567890";

// 後方文字列
NSString* substr = [str substringFromIndex:3];
NSLog(@"%@", substr);    // "4567890"

// 前方文字列
substr = [str substringToIndex:3];
NSLog(@"%@", substr);    // "123"

// 中間文字列
substr = [str substringWithRange:NSMakeRange(3, 3)];
NSLog(@"%@", substr);    // "456"
```

### Ruby

```
str = "1234567890"

# 後方文字列
puts str[3..-1] # "4567890"

# 前方文字列
puts str[0..2] # "123"
puts str[0, 3] # "123"

puts str[3..5] # "456"
puts str[3, 3] # "456"
```


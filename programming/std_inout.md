- Table of Content  
{:toc}

# 標準入力・出力

## 標準入力から 1行ずつ読み込む

Java
```java
try(BufferedReader reader = new BufferedReader(new InputStreamReader(System.in))) {
    for(;;) {
        final String line = reader.readLine();
        if(line == null) break;
        System.out.println(line);
    }
}
```

Go
```go
scanner := bufio.NewScanner(os.Stdin)
var line string
for scanner.Scan() {
    line = scanner.Text()
    fmt.Println(line)
}
```

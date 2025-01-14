- Table of Content  
{:toc}

# 標準入力・出力

## 標準入力から 1行ずつ読み込む

Go
```go
scanner := bufio.NewScanner(os.Stdin)
var line string
for scanner.Scan() {
    line = scanner.Text()
    fmt.Println(line)
}
```

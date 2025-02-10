- Table of Content  
{:toc}

# ラムダ式

## 引数を 1つ受け取り、戻り値を返す

ファイル名を受け取り、内容を返すラムダ式

### Java

```java
public String test() {
    String result = "";
    try {
        result = openFile("hoge.txt", (reader) -> {
            final StringBuilder sb = new StringBuilder();
            try {
                for (; ; ) {
                    final String line = reader.readLine();
                    if(line == null) break;
                    sb.append(line);
                    sb.append('\n');
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            return sb.toString();
        });
    } catch(IOException ex) {
        ex.printStackTrace();
    }

    return result;
}

private String openFile(final String filePath, final Function<BufferedReader, String> func) throws IOException {
    try(BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
        return func.apply(reader);
    }
}
```

例外を個別に処理せずまとめてスローしたい場合は、Java 標準で用意されている Function を使わず例外をスローするラムダ式のインターフェースを定義する。

```java
public String test() {
    String result = "";
    try {
        result = openFile("hoge.txt", (reader) -> {
            final StringBuilder sb = new StringBuilder();
            for (; ; ) {
                final String line = reader.readLine();
                if(line == null) break;
                sb.append(line);
                sb.append('\n');
            }
            return sb.toString();
        });
    } catch(Exception ex) {
        ex.printStackTrace();
    }
    
    System.out.println(result);
}

private String openFile(final String filePath, final MyFunction<BufferedReader, String> func) throws Exception {
    try(BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
        return func.apply(reader);
    }
}

@FunctionalInterface
public interface MyFunction<T,R> {
    R apply(T t) throws Exception;
}
```


### Kotlin

```kotlin
import java.io.BufferedReader
import java.io.FileReader

fun test() {
    val contents = openFile("hoge.txt") {
        val sb = StringBuilder()
        while(true) {
            val line = it.readLine() ?: break
            sb.append(line)
            sb.append('\n')
        }
        sb.toString()
    }

    println(contents)
}

fun openFile(filePath: String, f: (x:BufferedReader) -> String): String {
    BufferedReader(FileReader(filePath)).use {
        return f(it)
    }
}
```

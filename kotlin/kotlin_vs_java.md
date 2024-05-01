- Table of Content  
{:toc}

# Kotlin vs Java 

## エルビス演算子

```kotlin
val margin: Int = System.getenv("MARGIN")?.toInt() ?: 1
```

```java
int margin;
if(System.getenv("MARGIN") == null) {
    margin = 1;
} else {
    margin = Integer.parseInt(System.getenv("MARGIN"));
}
```

(予定)

## ラムダ式

## 拡張関数



- Table of Content  
{:toc}


# ラムダ式

## 簡単なラムダ式の例

```java
final String str = ((Supplier<String>) () -> {
  return "hoge";
).get();
System.out.println(str);
```

## ラムダ式で必要なデータをパラメーターで渡すか、クロージャーで参照するか

```java
final String str = "hoge";

// パラメーターで渡す
final int functionResult = ((Function<String, Integer>)(String s) -> {
    return s.length();
}).apply(str);
System.out.println(functionResult);

// クロージャーで参照
final int supplierResult = ((Supplier<Integer>) () -> {
    return str.length();
}).get();
System.out.println(supplierResult);
```

結果は同じ

## スレッド実行する処理をラムダ式で記述

```
new Thread( () -> { System.out.println("hoge"); } ).start()
```

# ラムダ式

## 簡単なラムダ式の例

```
final String str = ((Supplier<String>) () -> {
  return "hoge";
).get();
System.out.println(str);
```

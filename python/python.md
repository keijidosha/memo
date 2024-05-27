- Table of Content  
{:toc}

# Python

## 制御構造

### if

* if 文  
  ```python
  ii = 3

  if ii < 0:
    print('負')
  elif ii == 0:
    print('ゼロ')
  else:
    pirnt('正')
  ```
* == は同じ値かどうかを比較、is は参照が同じか(メモリ上の同じアドレスを指しているか?)をチェック
  ```python
  s1 = 'hoge' + str(1)
  s2 = 'hoge' + str(1)
  print(s1 == s2)
  # => True
  print(s1 is s2)
  # => False
  ```
* リストに含まれているかチェック
  ```python
  list = [1, 2, 3, 4, 5]
  ii = 3

  # 含まれているかチェック
  if ii in list:
    print('included')

  # 含まれていないかチェック
  if 6 not in list:
    print('not included')
  ```
* 空/ゼロチェック
  ```python
  # 整数
  ii = 0
  if ii:
    print('not zero')
  else:
    print('zero')
  # => zero
  # 0.0 も False として判定される

  # 文字列
  str = ''
  if str:
    print('not empty')
  else:
    print('empty')
  # => empty

  # リスト
  list = []
  if list:
    print('not empty')
  else:
    print('empty')
  # => empty
  # 空タプル (), 空ディクリショナリ {}, 空Set () も False として判定される
  ```

### while + else

while ループを抜けると else が実行される。  
(break で抜けた場合は else は実行されない。)
```python
idx = 1
sum = 0

while idx <= 10:
  if idx == 11:
    break
  sum += idx
  idx += 1
else:
  print(sum)
# => 55
```

### for

```python
sum = 0
for ii in range(10):
  sum += ii
else:
  print(sum)
# => 45
```
`range(10)` は `range(0, 10, 1)` と同じ  
0 から 1 ずつ増えて 9(10未満)で終わり。

* リストからインデックス付きで値を取り出す  
  enumerate を使う。
  ```python
  names = ['hoge', 'fuga']
  for idx, name in enumerate(names):
    print(idx, name)
  # => 0 hoge, 1 fuga
  ```
* ディクショナリからキーと値を取り出す  
  items を使う
  ```python
  dic = { 'id': 1, 'name': 'hoge' }
  for k, v in dic.items():
    print(k, v)
  # => id 1, name hoge
  ```
  items() がタプルを返し、それをアンパックして k, v に代入する。


## 文字列

* シングルクォート、ダブルクォートで囲った文字列の中にシングルクォートを書く  
  バックスラッシュでエスケープ  
  ```python
  print('It isn\'t xxx')
  print("It is \"hoge\"")
  ```
* バックスラッシュを特殊文字として扱わない(例: \n を改行文字として扱わない)  
  文字列の前に r を付ける(raw)  
  ```python
  print(r"That \n means new line")
  ```
* 文字列中に変数を埋め込む  
  文字列の前に f を付ける(f-strings)  
  ```python
  capital = 'Tokyo'
  country = 'Japan'
  print(f'Tha capital of {country} is {capital}')
  ```

### 長い文字列

* 長い文字列を複数行に分けて書く  
  カッコで囲む  
  ```python
  str = ('No1'
         'No2'
         'No3')
  ```
  行末にバックスラッシュを付ける  
  ```python
  str = 'No1'\
        'No2'\
        'No3'
  ```
* 複数行にわたる文字列  
  ダブルクォート 3つ(行末にバックスラッシュを付けると次の行に継続する)  
  ```python
  print("""No1
  No2
  No3""")
  # これは次のと同じ結果になる
  print("""\
  No1
  No2
  No3\
  """)
  ```

### 文字列のスライス(substring)

* 3番目の文字を取り出す  
  ```python
  str = "Hawaii"
  print(str[2])  # w
  ```
* 最後の文字を取り出す  
  ```python
  str = "Hawaii"
  print(str[-1])  # i
  ```
* 2〜4番目の文字列を取り出す  
  ```python
  str = "Hawaii"
  print(str[2:5])  # wai
  ```
* 最初の 3文字を取り出す
  ```python
  str = "Hawaii"
  print(str[:3])  # Haw
  ```
* 3文字目以降を取り出す
  ```python
  str = "Hawaii"
  print(str[2:])  # waii
  ```
* 最後の 3文字を取り出す
  ```python
  str = "Hawaii"
  print(str[-3:])  # aii
  ```
* 最後の 3文字より手前の文字列を取り出す
  ```python
  str = "Hawaii"
  print(str[:-3])  # Haw
  ```

## 計算

* 割り算の整数部分だけを取得  
  スラッシュを 2つ書く  
  ```python
  10 // 3  # => 3
  ```
* n乗する  
  アスタリンクを 2つ書く  
  ```python
  2 ** 8  # => 256
  ```

## データ構造

### リスト  

```pytohn
list = [1,2,3,4,5]
```

### タプル  

```python
tuple = (1,2,3,4,5)
```

* 空のタプル  
 ```python
 tuple = ()
 ```
* 要素が 1つだけのタプル  
 1つ目の要素の次にカンマを付けるとタプルとして認識される
 ```python
 tuple = (1,)
 ```
* タプルをアンパック
 ```python
 tuple = (1,2)
 hoge, fuga = tuple
 print(hoge, fuga)  # 1 2
 ```

### ディクショナリ  

```python
dic = { 'id': 1, 'name': 'hoge' }
# 別の書き方
dic2 = dict( id=1, name='hoge' )
dic3 = dict([('id', 1), ('name', 'hoge')])
```

* ディクリショナリから存在しないキーで取り出す
 ```python
 id = dic['identity']
 # => KeyError がスローされる
 id = dic.get('identity')
 print(type(id))  # <class 'NoneType'>
 # => get で取り出すと NoneType が返る
 print(id is None)
 # => True
 ```

### セット

```python
s = { 1, 2, 3, 2, 4, 5}
print(set)  # {1, 2, 3, 4, 5} => 重複した 2 が 1つにまとめられる
```

* リストからセットに変換してユニーク化
 ```python
 list = [ 1, 2, 3, 2, 4, 5 ]
 s = set(list)  # {1, 2, 3, 4, 5}
 ```

## 関数

### キーワード引数、デフォルト引数

```python
def func(hoge, fuga, foo=3):
  print("hoge=", hoge)
  print("fuga=", fuga)
  print("foo=", foo)

# キーワード引数で呼び出す
func(fuga="fuga!", hoge="hoge!", foo=1)
# 1番目は位置引数で、2つ目からはキーワード引数で呼び出す
func("hoge#", foo=1, fuga="fuga#")
# 1, 2番目は一引数で、3番目はデフォルト引数で呼び出す
func("hoge$", "fuga$")
```

デフォルト引数に空のリストをセットする場合、リストが再利用されることに注意する。
```python
def func(num, hoge=[]):
  hoge.append(num)  # 2回目以降に呼び出す時は、前回呼び出された時のリストに対して追加される。
  return hoge

list = func(1)
print(list)  # [1]
list = func(2)
print(list)  # [1, 2]
```

これを避けるには、デフォルト引数に None を指定し、関数内で None の場合にリストを初期化する。
```pyton
def func(num, hoge=None):
  if hoge is None:  # 何回呼び出されても、毎回引数が省略された場合はリストを初期化する。
    hoge = []
  hoge.append(num)
  return hoge

list = func(1)
print(list)  # [1]
list = func(2)
print(list)  # [2] <= 2だけになる
```


#### キーワード引数をディクショナリとして渡す

```python
def func(**args):
  print(args)
  for k, v in args.items():
    print(k, '=', v)

func(state='Hawaii', island='Oafu', city='Honolulu')
```

### 引数をタプルとして渡す

パラメーター名の前に * を付けるとタプルとして受け取る。

```python
def func(*args):
  print(args)
  state, island, city = args
  print('state=', state, ', island=', island, ', city=', city)

func('Hawaii', 'Oafu', 'Honolulu')
```

#### 位置引数とタプルを混在して渡す

```python
def func(state, *args):
  print('state=', state)
  for arg in args:
    print(arg)

func('Hawaii', 'Oafu', 'Honolulu')
```

### 位置引数, タプル, ディクショナリの引数を混在して渡す

```python
def func(country, *states, **args):
  print('country=', country)
  print('states=', states)
  for k, v in args.items():
    print(k, '=', v)

# USA は位置パラメーター, California, New York, Florida はタプル, 残りはディクショナリとして渡す。
func('USA', 'California', 'New York', 'Florida', state='Hawaii', island='Oafu', city='Honolulu')
```

## Tips

### スクリプトが直接起動された場合だけ、main を実行する。

```python
def main():
   # メイン処理

if __name__ == '__main__':
    main()
```
### 簡易 HTTP サーバーをたてる  
```
python -m http.server 8000
```
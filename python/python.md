- Table of Content  
{:toc}

# Python


### スクリプトが直接起動された場合だけ、main を実行する。

```python
def main():
   # メイン処理

if __name__ == '__main__':
    main()
```

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


## Tips

* 簡易 HTTP サーバーをたてる  
  ```
  python -m http.server 8000
  ```

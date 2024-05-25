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

## Tips

* 簡易 HTTP サーバーをたてる  
  ```
  python -m http.server 8000
  ```

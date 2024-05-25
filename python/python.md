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


## Tips

* 簡易 HTTP サーバーをたてる  
  ```
  python -m http.server 8000
  ```

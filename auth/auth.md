- Table of Content  
  {:toc}

# 認証

* JWTトークンをデコード
  ```
  echo <トークン> | cut -d . -f 2 | base64 -d | jq .
  ```


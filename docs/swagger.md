# swagger


## Tips

* Docker で swagger の yaml ファイルを HTML に変換。  
  ```
  docker run --rm -it -v /vagrant:/vagrant redoc bash
  redoc-cli bundle xxx-api.yaml
  ```

(参考) https://www.d-make.co.jp/blog/2021/03/09/openapi-yaml-html/

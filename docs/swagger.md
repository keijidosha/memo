# swagger


## Tips

* swagger editor を Docker で起動  
  ```
  docker run --name swagger -d -it --rm -p 8080:8080 swaggerapi/swagger-editor
  ```
* Docker で swagger の yaml ファイルを HTML に変換。  
  1. Docker 定義用ディレクトリ作成  
     ```
     cd ~/Documents/docker/
     mkdir redoc-cli
     cd redoc-cli
     ```
  1. Dockerfile 作成  
     ```
     FROM node

     RUN apt-get -y update

     RUN npm i -g npm
     RUN npm i -g @redocly/cli
     ```
  1. ビルド  
     ```
     docker build -t redoc-cli:0.0.1 .
     ```
  1. 実行  
     ```
     cd ~/Documents/
     docker run --rm -it -v$(pwd):/swagger redoc-cli:0.0.1 bash
     ```
  1. HTML生成  
     ```
     cd /swagger
     npx @redocly/cli build-docs openapi.yml
     ```  
     redoc-static.html が生成される。
     出力する HTML ファイル名を指定する場合は --output オプションを指定。
     ```
     npx @redocly/cli build-docs openapi.yml --output=hoge.html
     ```  
     (参考) [https://www.d-make.co.jp/blog/2021/03/09/openapi-yaml-html/](https://www.d-make.co.jp/blog/2021/03/09/openapi-yaml-html/)

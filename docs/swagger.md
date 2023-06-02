# swagger


## Tips

* swagger editor を Docker で起動  
  ```
  docker run --name swagger -d -it --rm -p 8080:8080 swaggerapi/swagger-editor
  ```
* Docker で swagger の yaml ファイルを HTML に変換。  
  * Docker 定義用ディレクトリ作成  
    ```
    cd ~/Documents/docker/
    mkdir redoc-cli
    cd redoc-cli
    ```
  * Dockerfile 作成  
    ```
    FROM node

    RUN apt-get -y update

    RUN npm i -g npm
    RUN npm i -g @redocly/cli
    ```
  * ビルド  
    ```
    docker build -t redoc-cli:0.0.1 .
    ```
  * 実行  
    ```
    cd ~/Documents/
    docker run --rm -it -v$(pwd):/swagger redoc-cli:0.0.1 bash
    ```
  * HTML生成  
    ```
    cd /swagger
    npx @redocly/cli build-docs openapi.yml
    ```  
    redoc-static.html が生成される。  
    (参考) [https://www.d-make.co.jp/blog/2021/03/09/openapi-yaml-html/](https://www.d-make.co.jp/blog/2021/03/09/openapi-yaml-html/)

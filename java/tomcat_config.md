# Tomcat 設定

## デプロイ

* 特定のWEBアプリを、webapps ディレクトリ以外に配置する
  ```
  <Context path="/hoge" docBase="/var/lib/hoge">
  </Context>
  ```

## 同時接続数を制限する

* サーバ全体で同時接続数を制限する  
  conf/server.xml
  ```
  <Server port="8005" shutdown="SHUTDOWN">
    <Service name="Catalina">
      <Engine name="Catalina" defaultHost="localhost">
        <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
          <Valve className="org.apache.catalina.valves.SemaphoreValve" block="true" concurrency="1" />
        </Host>
      </Engine>
    </Service>
  </Server>
  ```
* 特定のWEBアプリに対して同時接続数を制限する  
  conf/Catalina/localhost/<web-app-name>.xml
  ```
  <Context>
      <Valve className="org.apache.catalina.valves.SemaphoreValve" block="true" concurrency="1" />
  </Context>
  ```

## CLASSPATH

* 特定のWEBアプリに対して、特定のディレクトリにあるJARファイルを参照できるようにする
  conf/Catalina/localhost/<web-app-name>.xml  
  ```
  <Context path="/hoge">
      <Loader className="org.apache.catalina.loader.VirtualWebappLoader"
              virtualClasspath="/var/lib/kotlinc/lib/*.jar"/>
  </Context>
  ```


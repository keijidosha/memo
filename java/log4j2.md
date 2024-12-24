- Table of Content  
{:toc}

# Log4j2

## 初期設定(例)

### gradle

```
dependencies {
    implementation group: 'org.slf4j', name: 'slf4j-api',                        version: '2.0.16'
    implementation group: 'org.apache.logging.log4j', name: 'log4j-api',         version: '2.24.2'
    implementation group: 'org.apache.logging.log4j', name: 'log4j-slf4j2-impl', version: '2.24.2'
}
```

### 設定ファイル

src/main/resources に log4j2.xml というファイル名で配置。  
またはシステムプロパティ log4j.configurationFile に設定ファイルのパスを指定。  
(例) `-Dlog4j.configurationFile=/etc/log4j2.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE project>
<Configuration status="off" monitorInterval="60">

    <Properties>
        <Property name="format">[%d{yyyy/MM/dd HH:mm:ss.SSS}] %p : %m%n</Property>
        <Property name="logfile">logs/hoge.log</Property>
        <Property name="logfile-patten">logs/hoge.log.%i</Property>
    </Properties>


    <Appenders>
        <RollingFile name="logfile" fileName="${logfile}" filePattern="${logfile-patten}">
            <PatternLayout pattern="${format}" />
            <SizeBasedTriggeringPolicy size="10MB" />
            <DefaultRolloverStrategy fileIndex="min" min="1" max="10" />
        </RollingFile>
    </Appenders>

    <Loggers>
        <Root level="info">
            <AppenderRef ref="logfile" />
        </Root>
    </Loggers>
</Configuration>
```

### ログ出力

```Java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
(略)
final Logger logger = LoggerFactory.getLogger(getClass());
logger.info("Hello World");
```

## 設定

* ファイルサイズ(1MB)でローテーションして、10世代残す
  ```xml
  <Appenders>
      <RollingFile name="hoge" fileName="$logs/hoge.log" filePattern="logs/hoge.%i.log.gz">
          <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
          <SizeBasedTriggeringPolicy size="1MB" />
          <DefaultRolloverStrategy fileIndex="max" min="1" max="10" />
      </RollingFile>
  </Appenders>
  ```
  fileIndex に max を指定すると、ローテーション後の古いファイルほど連番の数字が大きくなる => /var/log/message.x と同じ感じ
* 日付でローテーションして、10日以上古いファイルを削除
  ```xml
  <Appenders>
      <RollingFile name="hoge" fileName="logs/hoge.log" filePattern="logs/hoge.%d{yyyy-MM-dd}.log">
          <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
          <TimeBasedTriggeringPolicy />
          <DefaultRolloverStrategy>
              <Delete basePath="logs" maxDepth="1">
                  <IfFileName glob="hoge*.log.gz" />
                  <IfLastModified age="10d" />
              </Delete>
          </DefaultRolloverStrategy>
      </RollingFile>
  </Appenders>
  ```
* 日付でローテーションして、10個まで残す
  ```xml
  <Appenders>
      <RollingFile name="hoge" fileName="logs/hoge.log" filePattern="logs/hoge.%d{yyyy-MM-dd}.log">
          <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
          <TimeBasedTriggeringPolicy />
          <DefaultRolloverStrategy>
              <Delete basePath="logs" maxDepth="1">
                  <IfFileName glob="hoge*.log.gz" />
                  <IfAccumulatedFileCount exceeds="10" />
              </Delete>
          </DefaultRolloverStrategy>
      </RollingFile>
  </Appenders>
  ```
* OS標準でないエンコーディングを指定して出力する場合は、Pattern に指定する
  ```xml
  <PatternLayout charset="Windows-31j" pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
  ```
* Marker でフィルターして、特定の情報だけをログ出力する
  ```xml
  <RollingFile name="hoge" fileName="logs/hoge.log" filePattern="logs/hoge.%i.log.gz">
      <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
      <SizeBasedTriggeringPolicy size="5MB" />
      <DefaultRolloverStrategy max="10" fileIndex="max" />
      <MarkerFilter marker="HOGE" onMatch="ACCEPT" onMismatch="DENY"/>
  </RollingFile>
  ```
  MarkerFilter を複数指定する場合は、Filters で囲む  
  ```xml
  <RollingFile name="hoge" fileName="logs/hoge.log" filePattern="logs/hoge.%i.log.gz">
      <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
      <SizeBasedTriggeringPolicy size="5MB" />
      <DefaultRolloverStrategy max="10" fileIndex="max" />
      <Filters>
        <MarkerFilter marker="HOGE" onMatch="ACCEPT" onMismatch="NEUTRAL"/>
        <MarkerFilter marker="FUGA" onMatch="ACCEPT" onMismatch="DENY"/>
      </Filters>
  </RollingFile>
  ```
  
  ```java
  MarkerManager.Log4jMarker hogeLogMarker = new MarkerManager.Log4jMarker( "HOGE" );
  
  logger.info( hogeLogMarker, message )
  ```
* ログレベルごとにログファイルを分けて出力  
  ログファイルA は INFO で、ログファイルB には DEBUG で出力。  
  「&lt;Root level="debug"&gt;」で Root 配下の Max ログ出力レベルを debug いかに制限した上で、配下の「ref="logfile-info"」をさらに info に制限するイメージか?
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE project>
  <Configuration status="off" monitorInterval="60">
      <Properties>
          <Property name="format">[%d{yyyy/MM/dd HH:mm:ss.SSS}] %p : %m%n</Property>
          <Property name="logfile">/var/log/hoge/hoge.log</Property>
          <Property name="logfile-patten">/var/log/hoge/hoge.log.%i</Property>
          <Property name="logfile-info">/var/log/hoge/hoge-info.log</Property>
          <Property name="logfile-patten-info">/var/log/hoge/hoge-info.log.%i</Property>
      </Properties>
  
      <Appenders>
          <RollingFile name="logfile" fileName="${logfile}" filePattern="${logfile-patten}">
              <PatternLayout pattern="${format}" />
              <SizeBasedTriggeringPolicy size="1MB" />
              <DefaultRolloverStrategy fileIndex="min" min="1" max="10" />
          </RollingFile>
          <RollingFile name="logfile-info" fileName="${logfile-info}" filePattern="${logfile-patten-info}">
              <PatternLayout pattern="${format}" />
              <SizeBasedTriggeringPolicy size="1MB" />
              <DefaultRolloverStrategy fileIndex="min" min="1" max="10" />
          </RollingFile>
      </Appenders>
  
      <Loggers>
          <Root level="debug">
              <AppenderRef ref="logfile-info" level="info" />
              <AppenderRef ref="logfile"     level="debug" />
          </Root>
      </Loggers>
  </Configuration>
  ```

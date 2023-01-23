# Log4j2

* ファイルサイズ(1MB)でローテーションして、10世代残す
  ```
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
  ```
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
  ```
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
  ```
  <PatternLayout charset="Windows-31j" pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
  ```
* Marker でフィルターして、特定の情報だけをログ出力する
  ```
  <RollingFile name="hoge" fileName="logs/hoge.log" filePattern="logs/hoge.%i.log.gz">
      <PatternLayout pattern="[%d{yyyy.MM.dd HH:mm:ss.SSS}] %p &lt;%c{1}&gt; %m%n" />
      <SizeBasedTriggeringPolicy size="5MB" />
      <DefaultRolloverStrategy max="10" fileIndex="max" />
      <MarkerFilter marker="HOGE" onMatch="ACCEPT" onMismatch="DENY"/>
  </RollingFile>
  ```
  
  ```
  MarkerManager.Log4jMarker hogeLogMarker = new MarkerManager.Log4jMarker( "HOGE" );
  
  logger.info( hogeLogMarker, message )
  ```


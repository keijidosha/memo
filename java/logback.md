
* 日付ごとに 1MB を超えるとローテーションする設定  
  ```
  <?xml version="1.0" encoding="UTF-8" ?>
  <configuration>
      <property name="LOG_DIR" value="/var/lib/hoge/logs" />
      <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <file>${LOG_DIR}/hoge.log</file>
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <fileNamePattern>${LOG_DIR}/hoge_%d{yyyy-MM-dd}.%i.log</fileNamePattern>
              <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                  <maxFileSize>1MB</maxFileSize>
              </timeBasedFileNamingAndTriggeringPolicy>
              <maxHistory>10</maxHistory>
              <cleanHistoryOnStart>true</cleanHistoryOnStart>
          </rollingPolicy>
          <encoder>
              <pattern>%-5r %d{yyyyMMdd HH:mm:ss.SSS} [%t] %-5le %lo{36} - %msg %n</pattern>
              <pattern>[%d{yyyy/MM/dd HH:mm:ss.SSS}] %le : %msg %n</pattern>
          </encoder>
      </appender>
  
      <root level="INFO">
          <appender-ref ref="FILE"/>
      </root>
  </configuration>
  ```
  * 起動時にすぐ上限(maxHistory)を超えて古くなったログファイルを削除  
    ```
    <cleanHistoryOnStart>true</cleanHistoryOnStart>
    ```

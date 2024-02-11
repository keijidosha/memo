# logback

- Table of Content  
{:toc}

## samples

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
* logback.xml を使わずに、ソースコードでレイアウト(フォーマット)を設定する
  ```java
  import ch.qos.logback.classic.PatternLayout;
  import ch.qos.logback.classic.spi.ILoggingEvent;
  import ch.qos.logback.core.ConsoleAppender;
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  
  public class PatternLayoutByJava {
      private final static Logger logger = LoggerFactory.getLogger(PatternLayoutByJava.class);
  
      public void run() {
          logger.info("Hello");  // 「10:15:25.123 [main] INFO PatternLayoutByJava -- Hello」

          // ルートロガーをリセットする。これをしないと後続の ConsoleAppender を追加しても、
          // ConsoleAppender とルートロガーの両方が出力されてしまう。
          // ルートロガーははずせない(delete できない)
          ch.qos.logback.classic.Logger rootLogger =
                  (ch.qos.logback.classic.Logger)LoggerFactory.getLogger(Logger.ROOT_LOGGER_NAME);
          rootLogger.getLoggerContext().reset();
  
          ch.qos.logback.classic.Logger backLogger = (ch.qos.logback.classic.Logger) logger;
  
          PatternLayout patternLayout = new PatternLayout();
          patternLayout.setPattern("%d{HH:mm:ss.SSS} %msg%n");    // 時刻とメッセージだけ出力する
          patternLayout.setContext(backLogger.getLoggerContext());
          patternLayout.start();
  
          ConsoleAppender<ILoggingEvent> consoleAppender = new ConsoleAppender<>();
          consoleAppender.setContext(backLogger.getLoggerContext());
          consoleAppender.setLayout(patternLayout);
          consoleAppender.start();
  
          backLogger.addAppender(consoleAppender);
  
          logger.info("Hello");      // 「10:15:25.123 Hello」
          backLogger.info("Hello");  // 「10:15:25.123 Hello」
      }
  }
  ```

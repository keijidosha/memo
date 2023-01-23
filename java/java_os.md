# OS

## OSによる Java 振る舞いの違い

* java.nio.file.WatchService
  * Linux: 変更があるとすぐに通知される
  * Mac: Java内部の実装クラスで定期的に監視しているため、監視間隔に到達するまで通知されない
    * com.sun.nio.file.SensitivityWatchEventModifier
      * LOW(30秒間隔), MEDIUM(10秒間隔), HIGH(2秒間隔)

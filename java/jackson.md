# Jackson

## annotation

* Json の項目名を別名にする  
(例) Java: itemId ==> Json: itemid
  ```kotlin
  import com.fasterxml.jackson.annotation.JsonProperty

  class Item {
      @JsonProperty( "itemid" )
      var itemId: String
  }
  ```
* Json を読み込む時、Json に存在しない項目は無視する(エラーにしない)  
  ```kotlin
  import com.fasterxml.jackson.annotation.JsonIgnoreProperties

  class Item {
      var id: String
      var name: String
      @JsonIgnoreProperties( ignoreUnknown=true )
      var remark: String?
  }
  ```
* null の場合は出力(シリアライズ)に含めない  
  ```kotlin
  import com.fasterxml.jackson.annotation.JsonInclude

  class Item {
      var id: String
      var name: String
      @JsonInclude( value = JsonInclude.Include.NON_NULL )
      var remark: String?
  }
  ```
* 日付・時刻の文字列のパースを有効にする(JSR310を有効にする)  
  (例) RFC8601 形式の文字列を OffsetDateTime に変換できるようにする。
  ```gradle
  implementation 'com.fasterxml.jackson.datatype:jackson-datatype-jsr310:2.15.3'
  ```
  ```java
  final JavaTimeModule javaTimeModule = new JavaTimeModule();
  final ObjectMapper mapper = JsonMapper.builder().addModule(javaTimeModule).build();
  ```
* Kotlin の data class のように、コンストラクタで渡す項目にアノテーションを指定する  
  * build.gradle に次の行を追加  
    `compile group: 'com.fasterxml.jackson.module', name: 'jackson-module-kotlin', version: '2.9.6'`  
    ※ 2.9.6 は使っている Jackson のバージョンを指定
  * コンストラクタの引数にアノテーションを指定  
    ```kotlin
    import com.fasterxml.jackson.annotation.JsonInclude

    data class Item(
        var id: String
        var name: String
        @JsonInclude( value = JsonInclude.Include.NON_NULL )
        var remark: String?
    )
    ```
  * KotlinModule を登録した ObjectMapper を使ってシリアライズ  
    ```kotlin
    val mapper = ObjectMapper().registerModule( KotlinModule())
    println( mapper.writeValueAsString( item ))
    ```




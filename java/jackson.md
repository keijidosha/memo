# Jackson

## annotation

* Json の項目名を別名にする  
(例) Java: itemId ==> Json: itemid
  ```
  import com.fasterxml.jackson.annotation.JsonProperty

  class Item {
      @JsonProperty( "itemid" )
      var itemId: String
  }
  ```
* Json を読み込む時、Json に存在しない項目は無視する(エラーにしない)  
  ```
  import com.fasterxml.jackson.annotation.JsonIgnoreProperties

  class Item {
      var id: String
      var name: String
      @JsonIgnoreProperties( ignoreUnknown=true )
      var remark: String?
  }
  ```
* null の場合は出力(シリアライズ)に含めない  
  ```
  import com.fasterxml.jackson.annotation.JsonInclude

  class Item {
      var id: String
      var name: String
      @JsonInclude( value = JsonInclude.Include.NON_NULL )
      var remark: String?
  }
  ```
* Kotlin の data class のように、コンストラクタで渡す項目にアノテーションを指定する  
  * build.gradle に次の行を追加  
    `compile group: 'com.fasterxml.jackson.module', name: 'jackson-module-kotlin', version: '2.9.6'`  
    ※ 2.9.6 は使っている Jackson のバージョンを指定
  * コンストラクタの引数にアノテーションを指定  
    ```
    import com.fasterxml.jackson.annotation.JsonInclude

    data class Item(
        var id: String
        var name: String
        @JsonInclude( value = JsonInclude.Include.NON_NULL )
        var remark: String?
    )
    ```
  * KotlinModule を登録した ObjectMapper を使ってシリアライズ  
    ```
    val mapper = ObjectMapper().registerModule( KotlinModule())
    println( mapper.writeValueAsString( item ))
    ```




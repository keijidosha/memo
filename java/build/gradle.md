# Gradle

## build.gradle 設定
* MANIFEST.MF に属性追加
  ```
  jar {
    manifest {
      attributes "Implementation-Version": "1.0"
    }
  }
  ```
* Fat JAR 作成  
  * createjfatJar タスクを追加して Fat JAR をビルド  
    ```
    task createfatJar(type: Jar) {
        baseName = 'ashAdapterSim2'
        duplicatesStrategy 'exclude'
        manifest {
            attributes 'Main-Class': 'jp.co.nextgen.ash.tester.ashadaptersim2.AshMain'
        }
        archiveClassifier = "all"
        from {
            configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
        }
        with jar
    }
    ```  
    * ビルド  
      `./gradlew createfatJar`
  * デフォルトの jar タスク定義を上書きして Fat JAR のビルド定義  
    ```
    jar {
        duplicatesStrategy 'exclude'
        manifest {
            attributes 'Main-Class': 'jp.co.nextgen.ash.tester.ashadaptersim2.AshMain'
        }
        from {
            configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
        }
    }
    ```
    * ビルド  
      `./gradlew jar`


## コマンド

* タスク一覧表示  
gradle tasks
* 実行  
gradle run
* 依存関係表示  
`gradle dependencies`  
実行時の依存関係だけを表示  
`gradle dependencies --configuration runtime`
* 配布用ファイルを作成  
`gradle assembleDist`
* コンパイル  
`gradle compileKotlin`  
`gradle compileJava`  
`gradle compileTestKotlin`  
`gradle compileTestJava`  
* JARファイル作成  
`gradle build`
  * テストをスキップ  
    `gradle build -x test`

## トラブル対応

* build ができない(IDLE 状態で止まる)  
ESET をインストールしている場合は、ファイアウォール設定を対話型に変更して、Java から外部への接続を適切に許可する。
* Gradle のキャッシュがおかしくなった  
`./gradlew clean build --refresh-dependencies`


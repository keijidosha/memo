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
  Gradle 7.2 で確認  
  * createjfatJar タスクを追加して Fat JAR をビルド  
    ```
    task createfatJar(type: Jar) {
        baseName = 'Fuga'
        duplicatesStrategy 'exclude'
        manifest {
            attributes 'Main-Class': 'com.example.Hoge'
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
            attributes 'Main-Class': 'com.example.Hoge'
        }
        from {
            configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
        }
    }
    ```
  * サンプル
    ```
    plugins {
      id 'java'
      id 'com.palantir.git-version' version '0.12.3'
    }

    group 'com.examples'
    version ''

    repositories {
        mavenCentral()
    }

    tasks.withType(JavaCompile).configureEach {
      options.deprecation = true
      options.compilerArgs << '-Xlint:unchecked'
    }

    def gitInfo = versionDetails()
    version = gitInfo.lastTag
    def implementationVersion = version + '(' + gitInfo.gitHash[0..7] + ')'

    jar {
      archiveBaseName = 'hoge'
      archiveVersion = ''
      duplicatesStrategy 'exclude'
      manifest {
        attributes(
                'Main-Class': 'com.examples.Hoge',
                'Implementation-Version' : implementationVersion,
                'Implementation-Title' : 'Hoge'
        )
      }
      from {
        configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
      }
    }

    dependencies {
      implementation group: 'org.slf4j', name: 'slf4j-api', version: '2.0.9'
      testImplementation 'org.junit.jupiter:junit-jupiter-api:5.8.1'
      testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.8.1'
    }

    test {
      useJUnitPlatform()
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


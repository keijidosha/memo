# Jooby Kotlin


* 最小ソース  
  ```
  import org.jooby.*

  fun main( args: Array<String> ) {
      run( ::App, *args )
  }

  class App: Kooby (
      {
          get {
              "Hello"
          }
      }
  )
  ```
* パラメータ取得  
  ```
  get { "reuqested parameter is ${param( "name" )}" }
  ```

build.gradle
```
buildscript {
    ext.kotlin_version = '1.2.60'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        /**
         * joobyRun
         */
        classpath group: 'org.jooby', name: 'jooby-gradle-plugin', version: '1.5.0'
    }
}

group 'Sample2'
version '1.0-SNAPSHOT'

apply plugin: 'kotlin'
apply plugin: 'application'

apply plugin: 'jooby'

mainClassName = "net.keiji.AppKt"
sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version"

    compile group: 'org.jooby', name: 'jooby', version: '1.5.0'
    compile group: 'org.jooby', name: 'jooby-netty', version: '1.5.0'
    compile group: 'org.jooby', name: 'jooby-lang-kotlin', version: '1.5.0'

    compile group:'io.netty', name: 'netty-transport-native-epoll', version: '4.1.28.Final'
}

/* * We diverge from the default resources structure to adopt the Jooby standard. */
sourceSets.main.resources {
    srcDirs = ["conf", "public"]
}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
```

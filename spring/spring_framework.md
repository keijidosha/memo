Table of Contents
=================

  * [DI](#DI)

# Spring

## DI

### 同じインターフェースを実行する 2つのクラスを DI 仕分ける。

* インターフェース MyBean を定義。  
  ```java
  public interface MyBean {
      void sayHello();
  }
  ```
* 実装クラスその 1  
  ```java
  @Component("Bean1")
  public class MyBean1 implements MyBean {
      @Override
      public void sayHello() {
          System.out.println(getClass().getName());
      }
  }
  ```
* 実装クラスその 2  
  ```java
  @Component("Bean2")
  public class MyBean2 implements MyBean {
      @Override
      public void sayHello() {
          System.out.println(getClass().getName());
      }
  }
  ```
* 実装クラスその 3  
  ```java
  @Component("Bean3")
  public class MyBean3 implements MyBean {
      @Override
      public void sayHello() {
          System.out.println(getClass().getName());
      }
  }
  ```
* 実装クラスその 4  
  ```java
  @Component("Bean4")
  public class MyBean4 implements MyBean {
      @Override
      public void sayHello() {
          System.out.println(getClass().getName());
      }
  }
  ```
* Bean 定義 XML ファイル
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">
      <bean id="XmlMyBean" class="com.example.test.di.bean.MyBean3" />
      <bean id="Bean4" class="com.example.test.di.bean.MyBean4" />
  </beans>
  ```
* コンフィグクラス  
  ```java
  @Configuration
  public class MyBeanConfig {
      @Bean
      // メソッド名とコンストラクターインジェクションのパラメーター名を合わせておく
      public MyBean myBean3() {
          return new MyBean3();
      }

      // Bean が返す型が ApplicationContext だと、サービスが AnnotationConfigApplicationContext を拾ってしまうので、より具体的な型を返す。
      @Bean
      public FileSystemXmlApplicationContext myApplicationContext() {
          // XML から Bean の定義を読み込む。
          return new FileSystemXmlApplicationContext( "file:/etc/di.xml");
      }
  }
  ```
* メインクラス  
  ```java
  public static void main(String[] args) {
      ConfigurableApplicationContext ctx = SpringApplication.run(MyApplication.class, args);
      
      MyService svc = ctx.getBean(MyService.class);
      svc.exec();
  }
  ```
* サービスクラスで Bean を仕分ける  
  ```java
  @Service
  public class MyService {
      private final MyBean myBean;
      private final MyBean myBeanHoge;
      // Spring がコンポーネントスキャンしたコンテキスト
      private final ApplicationContext context;
      // XML ファイルから読み込んだコンテキスト
      private final ApplicationContext xmlApplicationContext;
  
      public MyService(
              // コンストラクターインジェクションで ConfigurableApplicationContext を取得しておく。
              ApplicationContext context,
              // 型が ApplicationContext だと AnnotationConfigApplicationContext を引っ張ってくるので、より具体的な型を指定する。
              FileSystemXmlApplicationContext myApplicationContext,
              // @Qualifier で使用する Bean を指定。
              @Qualifier("Bean1")
              MyBean myBean,
              // コンフィグクラスの @Bean で定義したメソッド名とコンストラクターインジェクションのパラメーター名を合わせておく
              MyBean myBean3
    ) {
          this.context = context;
          this.myBean = myBean;
          this.myBeanHoge = myBean3;
          this.xmlApplicationContext = myApplicationContext;
      }
  
      public void exec() {
          // @Qualifier で指定して取得した Bean を実行。
          myBean.sayHello();
          // ConfigurableApplicationContext を使って Bean を指定。
          MyBean mb = context.getBean("Bean2", MyBean.class);
          mb.sayHello();
          myBeanHoge.sayHello();

          final MyBean mbXml1 = xmlApplicationContext.getBean("XmlMyBean", MyBean.class);
          mbXml1.sayHello();
          final MyBean mbXml2 = xmlApplicationContext.getBean("Bean4", MyBean.class);
          mbXml2.sayHello();

      }
  }
  ```
* 注意点
  * 異なるインターフェースを実装したクラス間であっても同じ Bean 名は付けれない。  
    Bean 名はユニークになっている必要がある。  


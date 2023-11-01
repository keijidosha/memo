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
* コンフィグクラス  
  ```java
  @Configuration
  public class MyBeanConfig {
      @Bean
      // メソッド名とコンストラクターインジェクションのパラメーター名を合わせておく
      public MyBean myBean3() {
          return new MyBean3();
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
      private final ConfigurableApplicationContext context;
  
      public MyService(
              // コンストラクターインジェクションで ConfigurableApplicationContext を取得しておく。
              ConfigurableApplicationContext context,
              // @Qualifier で使用する Bean を指定。
              @Qualifier("Bean1")
              MyBean myBean,
              // コンフィグクラスのメソッド名とコンストラクターインジェクションのパラメーター名を合わせておく
              MyBean myBean3
    ) {
          this.context = context;
          this.myBean = myBean;
          this.myBeanHoge = myBean3;
      }
  
      public void exec() {
          // @Qualifier で指定して取得した Bean を実行。
          myBean.sayHello();
          // ConfigurableApplicationContext を使って Bean を指定。
          MyBean mb = context.getBean("Bean2", MyBean.class);
          mb.sayHello();
          myBeanHoge.sayHello();
      }
  }
  ```
* 注意点
  * 異なるインターフェースを実装したクラス間であっても同じ Bean 名は付けれない。  
    Bean 名はユニークになっている必要がある。  


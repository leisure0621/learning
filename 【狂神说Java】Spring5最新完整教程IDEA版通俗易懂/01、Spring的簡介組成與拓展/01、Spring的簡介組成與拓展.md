<h1>目錄</h1>

- 1、[Spring](#s1)
  - 1-1、[Spring Framework (Spring 框架)](#s2)
  - 1-2、[Spring 官方教學文件與下載](#s3)
  - 1-3、[Spring 的組成](#s4)
  - 1-5、[Spring 的拓展](#s5)

---

# 1、Spring<span id="s1"/>

## 1-1、Spring Framework (Spring 框架)<span id="s2"/>

<img src="./image/21.dio.svg"/>


- **導入 [Spring Web MVC](https://mvnrepository.com/artifact/org.springframework/spring-webmvc) 就會引入很多其他包**


```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.6</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.6</version>
</dependency>
```

> 總結: **Spring 就是一個輕量級的控制反轉(IOC)和面相切面編程(AOP)的框架!**

## 1-2、Spring 官方教學文件與下載<span id="s3"/>

- **[官方文件](https://docs.spring.io/spring-framework/docs/current/reference/html/overview.html#overview): 官網 > Projects > Spring Framework > Reference Doc.**

<img src="./image/01.dio.svg"/>

- **官方下載**: (一般直接用 maven 下載就好)
  - **步驟一**: 網址改為 {Spring Domain}/spring-framework/docs/4.3.9.RELEASE
    - https://docs.spring.io/spring-framework/docs/4.3.9.RELEASE/spring-framework-reference/html/overview.html#overview-distribution-zip
  - **步驟二**: 進入下方網址後即有所有版本的 spring
    - http://repo.spring.io/release/org/springframework/spring
- **Spring GitHub**:
  - https://github.com/spring-projects/spring-framework

## 1-3、Spring 的組成<span id="s4"/>

- [Spring 由以下七大核心模块組成](./doc/Spring_七大核心模块.html)

<img src="./image/02.dio.svg"/>

## 1-5、Spring 的拓展<span id="s5"/>

- **在 Spring 的官網有這個介紹: 現代化的 Java 開發! 說白就是基於 Spring 的開發!**

<img src="./image/03.dio.svg"/>

<img src="./image/22.dio.svg"/>

- **因為現在大多數公司都在使用 Spring Boot 進行快速開發，<span style="color:red">學習 Spring Boot 的前提，需要完全掌握Spring 及 Spring MVC!</span> 承上啟下的作用!**

- **弊端: 發展太久後違背了原來的理念!配置十分繁瑣，人稱 <span style="color:red">"配置地獄!"</span>**

---


- [【狂神說Java】1、Spring 簡介](https://www.bilibili.com/video/BV1WE411d7Dv?p=1&spm_id_from=pageDriver)
- [【狂神說Java】2、Spring 組成及拓展](https://www.bilibili.com/video/BV1WE411d7Dv?p=2&spm_id_from=pageDriver)

---

- [Spring Framework](https://zh.wikipedia.org/wiki/Spring_Framework)
- [spring框架](https://baike.baidu.com/item/spring%E6%A1%86%E6%9E%B6/2853288)
- [【Spring】Spring简介、组成及拓展](https://blog.csdn.net/Aibiabcheng/article/details/116012859)
- [SSH](https://zhuanlan.zhihu.com/p/52953841)
- [Spring---七大核心模块](https://www.cnblogs.com/qlqwjy/p/9071510.html)
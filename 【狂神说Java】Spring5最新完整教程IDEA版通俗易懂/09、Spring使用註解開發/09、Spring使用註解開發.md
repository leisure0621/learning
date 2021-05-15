<h1>目錄</h1>

- 9、[Spring 使用註解開發](#s1)
  - 9-1、[xml 配合"註解"開發](#s2)
  - 9-2、[xml 配合"註解"實例](#s3)
  - 9-3、[使用 Java 的方式配置 Spring](#s4)
  - 9-4、[使用 Java 的方式配置 Spring 實例](#s5)

---

# 9、Spring 使用註解開發 <span id="s1"/>

## 9-1、xml 配合"註解"開發 <span id="s2"/>

- **在 Spring4 之後，要使用註解開發，必須要保證 aop 的包導入了**

<img src="./image/17.dio.svg"/>

- **使用註解需要導入 context (application.xml) 約束，增加註解的支持!**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

</beans>
```

- **Bean 注入 : 將某個類註冊到 Spring 中，裝配 Bean !**
  - **POJO** : @Component
  - **DAO** : @Repository
  - **Service** : @Service
  - **Controller** : @Controller

```xml
<!-- 設定檔(application.xml)中需加入要掃描的包，註解與設定檔需搭配使用-->
<context:component-scan base-package="com.kuang"/>
```

```java
// 註冊方法
@Component
public class User {}
```

- **<a href="./doc/Scope_prototype.html">Scope</a> 作用域 :**
  - **prototype** : 每次獲得 bean 都會生成一個新對象
  - **singleton** : 不會生成新對象

```java
@Component
@Scope("prototype")
public class User {}
```

- **小結** :
  - **xml 與 註解** :
    - **xml 更加萬能，適用於任何場合 ! 維護簡單方便**
    - **註解不是自己的類用不了，維護相對複雜 !**
  - **xml 與 註解 最佳實踐** :
    - **xml 用來管理 bean**
    - **註解只負責完成<u>屬性</u>的注入**
    - **我們在使用的過程中，只需要注意一個問題: 必須讓註解生效，就需要<u>開啟註解的支持</u>**

```xml
<context:component-scan base-package="com.kuang"/>
<context:annotation-config/>
```

## 9-2、xml 配合"註解"實例 <span id="s3"/>

- **目錄**

<img src="./image/18.dio.svg"/>

- **創建 application.xml** (導入 context 約束)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 指定要掃描的包，這個包下的註解就會生效 -->
    <!-- com.kuang 表示掃描此 package 下的所有物件，即會掃描到 controller、dao、pojo、service -->
    <!-- com.kuang.dao 表示掃描 dao 下的所有 -->
    <context:component-scan base-package="com.kuang"/>
    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

</beans>
```

<img src="./image/19.dio.svg"/>

- **創建 User.java**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

// bean 注入: 將某個類註冊到 Spring 中，裝配 Bean
// @Component 組件，等價於在 application.xml 中設定 (功能與 @Repository、@Service、@Controller 相同)
//  <bean id="user" class="com.kuang.pojo.User"/>
@Component
// scope 作用域 :
// @Scope("prototype") 表示每宣告一個 bean 就會創造一個新對象
// 等價於 <bean id="user" class="com.kuang.pojo.User" scope="prototype"/> 
@Scope("prototype")
public class User {

    public String firstName = "王";

    // 屬性注入:
    // @Value("大明") 等價於在 application.xml 中設定
    //  <bean id="user" class="cm.kuang.pojo.User">
    //      <property name="name" value="大明"/> <!-- 相當於此句 -->
    //  </bean>
    @Value("大明")
    public String lastName;

    public String nickName;

    // 屬性注入:
    //  直接在 set 上做的設定 (常用方法)
    @Value("隔壁老王")
    public void setNickName(String nickName) {
        this.nickName = nickName;
    }

}
```

- **創建 UserService.java**
  
```java
package com.kuang.service;

import org.springframework.stereotype.Service;

// bean 注入: 將某個類註冊到 Spring 中，裝配 Bean
// @Service 組件 (功能與 @Component、@Repository、@Controller 相同)
@Service
public class UserService {
}
```

- **創建 UserController.java**

```java
package com.kuang.controller;

import org.springframework.stereotype.Controller;

// bean 注入: 將某個類註冊到 Spring 中，裝配 Bean
// @Controller 組件 (功能與 @Component、@Repository、@Service 相同)
@Controller
public class UserController {
}
```

- **創建 UserDao.java**

```java
package com.kuang.dao;

import org.springframework.stereotype.Repository;

// bean 注入: 將某個類註冊到 Spring 中，裝配 Bean
// @Repository 組件，用於 dao 上 (功能與 @Component、@Service、@Controller 相同)
@Repository
public class UserDao {
}
```

- **測試類**

```java
import com.kuang.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        User user = context.getBean("user", User.class);
        System.out.println(user.firstName + user.lastName); // 王大明
        System.out.println(user.nickName); // 隔壁老王
    }
}
```

## 9-3、使用 Java 的方式配置 Spring <span id="s4"/>

- **我們現在要完全不使用 Spring 的 xml 配置了，全權交給 Java 來做 !**
- **JavaConfig 是 Spring 的一個子項目，在 Spring 4 之後，他成為了一個核心功能 !**
- **這種純Java 的配置方式，在 SpringBoot 中隨處可見 !**


## 9-4、使用 Java 的方式配置 Spring 實例 <span id="s5"/>

- **目錄**

<img src="./image/20.dio.svg"/>

- **創建 User.java**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

// 這裡這個註解的意思，就是說明這個類被 Spring 接管了，註冊到了容器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    // 屬性注入值
    @Value("王大明")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

- **創建 KuangConfig.java，作用是替代 applicatinConfig**

```java
package com.kuang.config;

import com.kuang.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

// @Configuration 這個也會被 Spring 容器託管，註冊到容器中，因為他本來就是一個 @Component
// @Configuration 代表這是一個配置類，等同 applicationContext.xml
@Configuration
// @ComponentScan 等同 <context:component-scan base-package="com.kuang.pojo"/>
@ComponentScan("com.kuang.pojo")
// @Import，將兩個配置類合一，等同 <import resource="KuangConfig2.xml"/>
@Import(KuangConfig2.class)
public class KuangConfig {

    // 註冊一個 bean，就相當於我們之前寫的一個 bean 標籤，等同 <bean id="getUser" class="com.kuang.pojo.User"/>
    // 這個方法的名子，就相當於 bean 標籤中的 id 屬性
    // 這個方法的返回值，就相當於 bean 標籤中的 class 屬性
    @Bean
    public User getUser(){
        // 就是返回要注入到 bean 的對象 !
        return new User();
    }

}
```

- **創建 KuangConfig2.java，未來要寫不同的 config.xml 不同合作不同設定時用 (主要是看何檔 @Import)**

```java
package com.kuang.config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class KuangConfig2 {}
```

- **測試類**

```java
import com.kuang.config.KuangConfig;
import com.kuang.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyTest {
    @Test
    public void test(){
        // 如果完全使用了配置類方式去做，我們就只能禿過 AnnotationConfig 上下文來獲取容器，通過配置類的 class 對象加載 !
        ApplicationContext context = new AnnotationConfigApplicationContext(KuangConfig.class);
        User getUser = context.getBean("getUser", User.class);

        System.out.println(getUser.getName()); // 王大明
    }
}
```

---

- [【狂神說Java】14、Spring 註解開發](https://www.bilibili.com/video/BV1WE411d7Dv?p=14&spm_id_from=pageDriver)
- [【狂神說Java】15、使用 JavaConfig 進行配置](https://www.bilibili.com/video/BV1WE411d7Dv?p=15&spm_id_from=pageDriver)
- [【狂神說Java】16、上周內容回顧](https://www.bilibili.com/video/BV1WE411d7Dv?p=16&spm_id_from=pageDriver)

---

- [SSM （SSM 框架集）](https://baike.baidu.com/item/SSM/18801167)
- [Java-based Container Configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java)
<h1>目錄</h1>

- 3、[HelloSpring](#s1)
  - 3-1、[開發前準備](#s2)
  - 3-2、[控制反轉 HelloWorld](#s3)
  - 3-3、[修改"IOC 理論推導"中的代碼並實現具體IOC](#s4)
  - 3-4、[問題思考](#s5)

---

# 3、HelloSpring<span id="s1"/>

## 3-1、開發前準備<span id="s2"/>

- **工具 : IDEA**
- **JDK : jdk1.8.0_231**
- **Maven : apache-maven-3.6.3**

## 3-2、控制反轉 HelloWorld<span id="s3"/>

- **目錄**

<img src="./image/02.dio.svg"/>

- **Project 右鍵 > New > Model > Maven > Next > Name > Finish**
  - **Name**: spring-02-hellospring

- **創建 Hello.java**
  
```java
package com.kuang.pojo;

public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

- **建立 beans.xml (xml 名稱可自訂)**
  - xml 範例取自官方文檔
    - [Spring Framework 中文文檔](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans)
    - [Spring 官方文檔](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 使用 Spring 來創建對象，在 Spring 這些都稱為 Bean
    類型 變量名 = new 類型();
    Hello hello = new Hello();

    id = 變量名
    class = new 的對象
    property 相當於給對象中的屬性設置一個值
    -->
    <bean id="hello" class="com.kuang.pojo.Hello">
        <property name="str" value="Spring"></property>
    </bean>
</beans>
```

- **創建 MyTest.java (測試類)**
  
```java
import com.kuang.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // 獲取 Spring 的上下文對象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        // 我們的對象現在都在 Spring 中管理了，我們要使用，直接去裡面取出來就可以
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```

## 3-3、修改"IOC 理論推導"中的代碼並實現具體IOC<span id="s4"/>

- **目錄**

<img src="./image/03.dio.svg"/>

- **建立 beans.xml (xml 名稱可自訂，目的是將對象改由Spring創建)**
  - xml 範例取自官方文檔
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="mysqlImpl" class="com.kuang.dao.UserDaoMySqlImpl"/>
    <bean id="oracleImpl" class="com.kuang.dao.UserDaoOracleImpl"/>

    <bean id="UserServiceImpl" class="com.kuang.service.UserServiceImpl">
        <!--
        ref: 引用 Spring 容器中創建好的對象
        value: 具體的值，基本數據類型!
        -->
        <property name="userDao" ref="mysqlImpl"/>
    </bean>

</beans>
```

- **修改 <u>MyTest.java</u>**

```java
import com.kuang.service.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // 獲取 ApplicationContext: 拿到 Spring 的容器
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        // 容器在手，天下我有，需要什麼，就直接 get 什麼
        UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");

        userServiceImpl.getUser();
    }
}
```

> 設定過後，未來只要修改配置文件就可以不動到主程式的情況下修改配置。假設 beans.xml 中 UserServiceImpl 要改使用 oracleImpl 的話，就將 property 的 ref 修改為 oracleImpl 就好

## 3-4、問題思考<span id="s5"/>

- **Q: Hello 對象是誰創建的?**
  **A: hello 對象由 Spring 創建的**
- **Q: Hello 對象的屬性是怎麼設置的?**
  **A: hello 對象的屬性是由 Spring 容器設置的**

- **這個過程就叫做控制反轉**:
  - **控制**: 誰來控制對象的創建，傳統應用程序的對象是由程序本身控制創建的，使用 Spring 後，對象是由 Spring 來創建的
  - **反轉**: 程序本身不創建對象，而變成被動的接收對象
  - **依賴注入**: 就是利用 set 方法來進行注入的。
- **IOC是一種編程思想，由主動的編程變成被動的接收。可以通過 newClassPathXmlApplicationContext 去瀏覽一下底層源碼。**
- **OK，到了現在，我們徹底不用在程序中去改動了，要實現不同的操作，只需要在xml配置文件中進行修改，所謂 <span style="color:red">IOC 一句話搞定，對象由 Spring 來創建、管理、裝配!</span>**

<img src="./image/01.dio.svg"/>

---

- 參考狂神影片 (5)
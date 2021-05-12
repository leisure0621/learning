<h1>目錄</h1>

- 5、[Spring 之 Bean 配置說明](#s1)
  - 5-1、[Bean 之 alias](#s2)
  - 5-2、[Bean 之 id、class、name 說明](#s3)
  - 5-3、[Bean 中的 import](#s4)

---

# 5、Spring 之 Bean 配置說明<span id="s1"/>

# 5-1、Bean 之 alias<span id="s2"/>

<img src="./image/01.dio.svg"/>

- **alias 又稱 [別名](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanname-alias)的配置**

```xml
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg name="name" value="秦疆"/>
</bean>

<bean id="userT" class="com.kuang.pojo.UserT">
</bean>

<!-- 別名，如果配置了別名，我們也能從別名獲取到對象 -->
<alias name="user" alias="userNew"/>
```

- **MyTest.java 修改**

```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
User user = (User) context.getBean("user");
User user2 = (User) context.getBean("userNew");
user.show();
user2.show();

System.out.println(user == user2);
```

> 結論: 不管是使用 user 或是 userNew 都可獲取到對象

## 5-2、Bean 之 id、class、name 說明<span id="s3"/>

- **beans.xml 配置說明**

```xml
<!--
id: bean 的唯一標示符，也就是相當於我們學的對象名
class: bean 對象所對應的全限定名: 包名 + 類型
name: 也是別名，且 name 可以同時取多個別名 (可以用空白，逗號，分號做分隔)
-->
<bean id="userT" class="com.kuang.pojo.UserT" name="user2 u2, u3;u4">
    <property name="name" value="西部開源"/>
</bean>
```

- **java 演示**

```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
UserT user = (UserT) context.getBean("user2");
UserT user2 = (UserT) context.getBean("u2");
UserT user3 = (UserT) context.getBean("u3");
UserT user4 = (UserT) context.getBean("u4");
user.show();
user2.show();

System.out.println(user == user2);
```

> 結論: 不使用 alias 就可以做多個別名設定

## 5-3、Bean 中的 import<span id="s4"/>

> 這個 import，一般用於團隊開發使用，他可以將多個配置文件導入併為一個
> 假設，現在項目中有多個人開發，這三人複製不同的類開發，不同的類需要註冊在不同的 bean 中，我們可以利用 import 將所有人的 beans.xml 合併為一個總的!
>  - 張三
>  - 李四
>  - 王五
>  - applicationContext.xml
> 使用的時候，直接使用總的配置就可以了
> 有重複的別名，會覆蓋

- **目錄結構**

<img src="./image/02.dio.svg"/>

- **beans.xml 配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.kuang.pojo.User">
        <constructor-arg name="name" value="秦疆"/>
    </bean>

    <bean id="userT" class="com.kuang.pojo.UserT"/>

    <alias name="user" alias="userNew"/>
</beans>
```

- **testBean.xml 配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="user" class="com.kuang.pojo.User" name="user2 u2, u3;u4; u5">
        <constructor-arg name="name" value="秦疆2"/>
    </bean>

</beans>
```

- **applicationContext.xml，import 其他 bean**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="beans.xml"/>
    <import resource="testBean.xml"/>

</beans>
```

- **MyTest.java 修改**

```java
import com.kuang.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // 將 beans 改為 applicationContext
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = (User) context.getBean("user");
        User user2 = (User) context.getBean("userNew");
        user.show(); // 秦疆2
        user2.show(); // 秦疆2

        System.out.println(user == user2);
    }
}
```

> 結論: 每個人可以做各自的 bean 設定，在團隊合作中常用

---

- 參考狂神影片 (7)
- [建構子（英語： Constructor，有時簡稱 ctor），別稱：構造方法、建構函式、建構子](https://zh.wikipedia.org/wiki/%E6%9E%84%E9%80%A0%E5%99%A8)
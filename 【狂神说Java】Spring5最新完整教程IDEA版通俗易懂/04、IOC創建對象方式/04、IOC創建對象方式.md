<h1>目錄</h1>

- 6、[IOC創建對象方式](#s1)
  - 6-1、[創建對象方式](#s2)
  - 6-2、[實際測試](#s3)

---

# 6、IOC創建對象方式<span id="s1"/>

## 6-1、創建對象方式<span id="s2"/>

1. **使用無參構造創建對象 (默認!)**
2. **假設我們要使用有參構造創建對象**
   1. **下標賦值**
   2. **類型**
   3. **參數名**

```xml
<!-- 下標賦值 -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg index="0" value="狂神說Java"/>
</bean>

<!-- 通過類型創建，不建議使用 (當建構子不只賦值一項，且都為同一類型的時候會出錯) -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg type="java.lang.String" value="qinjiang"/>
</bean>

<!-- 直接通過參數名賦值 -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg name="name" value="秦疆"/>
</bean>
```

## 6-2、實際測試<span id="s3"/>

- [目錄結構](https://ouoholly.github.io/post/command-folder-directory-tree-structure/#Options-meaning)

<img src="./image/01.dio.svg"/>

- **創 User.java**

```java
package com.kuang.pojo;

public class User {
    private String name;

    public User(String name){
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void show(){
        System.out.println("name="+name);
    }
}
```

- **創 UserT.java**

```java
package com.kuang.pojo;

public class UserT {
    private String name;

    public UserT(){
        System.out.println("UserT被創建了");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

- **建立空的 applicationContext.xml**

- **建立 beans.xml**

```xml
<!-- 方法1: 下標賦值 -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg index="0" value="狂神說Java"/>
</bean>

<!-- 方法2: 通過類型創建，不建議使用 (當建構子不只賦值一項，且都為同一類型的時候會出錯) -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg type="java.lang.String" value="qinjiang"/>
</bean>

<!-- 方法3: 直接通過參數名賦值 -->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg name="name" value="秦疆"/>
</bean>

<bean id="userT" class="com.kuang.pojo.UserT">
</bean>
```

- **建立 MyTest.java**

```java
import com.kuang.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // Spring 容器，就類似於婚介網站!
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user");
        User user2 = (User) context.getBean("user");
        /*
            UserT被創建了
            name=秦疆
        */
        user.show();

        System.out.println(user == user2); // true
    }
}
```

> 總結: 在配置文件加載的時候，容器中管理的對象就已經初始化了!


---

- 參考狂神影片 (6)
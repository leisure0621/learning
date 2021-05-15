<h1>目錄</h1>

- 7、[Bean的作用域](#s1)
  - 7-1、[單例模式](#s2)
  - 7-2、[原型模式](#s3)
  - 7-3、[其餘的 request、session、application](#s4)

---

# 7、Bean的作用域<span id="s1"/>

- **Bean scopes**

<img src="./image/16.dio.svg"/>

## 7-1、單例模式<span id="s2"/>

- **Spring 默認[單例模式](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype)機制**

```xml
<bean id="user2" class="com.kuang.pojo.User" c:name="狂神" c:age="18" scope="singleton"/>
```

- **MyTest.java**

```java
import com.kuang.pojo.Student;
import com.kuang.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.toString());
    }

    @Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
        // 單例模式，每次去 get 的時候都是同一個對象
        User user = context.getBean("user2", User.class);
        User user2 = context.getBean("user2", User.class);
        System.out.println(user.hashCode());
        System.out.println(user2.hashCode());
        System.out.println(user == user2);
    }
}
```

- **執行結果**

```cs
2074185499
2074185499
true
```


## 7-2、原型模式<span id="s3"/>

- **[原型模式](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype): 每次從容器中 get 的時候，都會產生一個新的對象!**

```cs
<bean id="user2" class="com.kuang.pojo.User" c:name="狂神" c:age="18" scope="prototype"/>
```

- **MyTest.java**

```java
import com.kuang.pojo.Student;
import com.kuang.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.toString());
    }

    @Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
        // 原型模式，每次去 get 的時候會產生一個新的對象
        User user = context.getBean("user2", User.class);
        User user2 = context.getBean("user2", User.class);
        System.out.println(user.hashCode());
        System.out.println(user2.hashCode());
        System.out.println(user == user2);
    }
}
```

- **執行結果**
  
```cs
797925218
275310919
false
```

## 7-3、其餘的 request、session、application<span id="s4"/>

- **這些只能在 web 開發中使用到!** (配置 **[監聽器...](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-other-web-configuration)** 等等)

---

- [【狂神說Java】11、Bean 的作用域](https://www.bilibili.com/video/BV1WE411d7Dv?p=11&spm_id_from=pageDriver)

---

- [單例模式 Singleton](https://skyyen999.gitbooks.io/-study-design-pattern-in-java/content/singleton.html)
- [【D6 - 三國鼎立時代】三大前端時代 -- 為何我選擇了Vue?](https://ithelp.ithome.com.tw/articles/10234520)
- [[翻譯] ANGULAR VS REACT VS VUE](https://terrylee7788.wordpress.com/2019/10/24/angular-vs-react-vs-vue/)
- [TypeScript 10分鐘快速入門](https://eddychang.me/typescript-quick-start-in-10-mins)
- [[Angular 深入淺出三十天] Day 02 - 為什麼是 Angular？](https://ithelp.ithome.com.tw/articles/10202827)
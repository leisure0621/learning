<h1>目錄</h1>

- 8、[Bean的自動裝配](#s1)
  - 8-4、[使用註解實現自動裝配](#s2)
    - 8-4-1、[@Autowired](#s3)
    - 8-4-2、[@Autowired(required = false)](#s4)
    - 8-5-3、[@Autowired 配合 @Qualifier](#s5)
    - 8-5-4、[@Nullable](#s6)
    - 8-5-5、[@Resource](#s7)
    - 8-5-6、[註解實現自動裝配總結](#s8)

---

# 8、Bean的自動裝配<span id="s1"/>

- **自動裝配是 Spring 滿足 bean 依賴的一種方式!**
- **Spring 會在上下文中自動尋找，並自動給 bean 裝配屬性!**
- **在 Spring 中有三種裝配的方式** ([範例下載](./doc/spring-05-autowired.zip))
  1. 在 xml 中顯示的配置
  2. 在 java 中顯示配置
  3. 隱式的自動裝配 bean 【重要!】

- **目錄**

<img src="./image/01.dio.svg"/>

- **Cat.java**

```java
package com.kuang.pojo;

public class Cat {
    public void shout(){
        System.out.println("miao");
    }
}
```

- **Dog.java**

```java
package com.kuang.pojo;

public class Dog {
    public void shout(){
        System.out.println("wang");
    }
}
```

- **people.java**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.lang.Nullable;

import javax.annotation.Resource;

public class People {

    private Cat cat;
    private Dog dog;
    private String name;

    public People() {}

    public Cat getCat() {
        return cat;
    }

    public Dog getDog() {
        return dog;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

- **beans.xml，<span style="color:red">ByName 自動裝配</span>**

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

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="cat" class="com.kuang.pojo.Cat"/>
    <bean id="dog" class="com.kuang.pojo.Dog"/>

    <!--
        byName: 會自動在容器上下文中查找，和自己對向 set 方法後面的值對應的 beanid ! id 不可省略 !

        // 有 dog 所以可以與上方的 dog 做對應
        public void setDog(Dog dog) {
            this.dog = dog;
        }
    -->
    <bean id="people" class="com.kuang.pojo.People" autowire="byName">
        <property name="name" value="小狂神呀"/>
    </bean>

</beans>
```

- **beans2.xml，<span style="color:red">ByType 自動裝配</span>**

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

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="cat" class="com.kuang.pojo.Cat"/>
    <!-- 不可有複數 com.kuang.pojo.Dog -->
    <bean class="com.kuang.pojo.Dog"/>

    <!--
        byType: 會自動在容器上下文中查找，和自己對向 屬性類型相同的 bean ! 但須全局唯一 ! 且 id 可省略 !

        // 有 Dog 所以可以與上方的 com.kuang.pojo.Dog 做對應
        public void setDog(Dog dog) {
            this.dog = dog;
        }
    -->
    <bean id="people" class="com.kuang.pojo.People" autowire="byType">
        <property name="name" value="小狂神呀"/>
    </bean>

</beans>
```

- **MyTest.java**

```java
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.kuang.pojo.People;

public class MyTest {
    @Test
    public void test1(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        People people = context.getBean("people", People.class);
        System.out.println(people.toString());
    }

    @Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");
        People people = context.getBean("people", People.class);
        System.out.println(people.toString());
    }
}
```



> byName 的時候，需要保證**所有 bean 的 id 唯一**，並且這個 bean 需要和自動注入的**屬性的 set 方法的值**一致 !
> byType 的時候，需要保證**所有 bean 的 class 唯一**，並且這個 bean 需要和自動注入的**屬性的類型**一致 !

## 8-4、使用註解實現自動裝配<span id="s2"/>

- **官方說明**: [Annotation-based Container Configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)
- jdk1.5 支持的註解，Spring2.5就支持註解了 ! 
- The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.
- **要使用註解須知**:
  - **導入約束**: context
  - **配置註解的支持**: context:annotation-config 【重要】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

- **@Autowired 直接在屬性上使用即可 ! 也可以在 set 方式上使用 !**
- **使用 Autowired 我們可以不用編寫 Set 方法了，前提是你這個自動裝配的屬性在 IOC (Spring) 容器中存在，且符合名子 byName!**

```java
public class People {

    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    
}
```

```xml
<bean id="cat" class="com.kuang.pojo.Cat"/>
<bean id="dog" class="com.kuang.pojo.Dog"/>
<bean id="people" class="com.kuang.pojo.People"/>
```

- **@Nullable 字段標記了這個註解，說明這個字段可以為 null**

```java
public class People {

    private Cat cat;
    private Dog dog;
    private String name;
    
    // 表示可以空值
    public People(@Nullable String name) {
        this.name = name;
    }

}
```

```xml
<bean id="people" class="com.kuang.pojo.People"/>
```

- **如果使用 @Autowired 自動裝配的環境比較複雜，自動裝配無法通過一個註解 【@Autowired】 完成的時候，我們可以使用 @Qualifier(value="xxx") 去配合 @Autowired 的使用，指定一個唯一的 bean 對象注入 !**

```java
public class People {

    // 如果顯示定義了 Autowired 的 required 屬性為 false，說明這個對象可以為 null，否則不允許為空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    @Qualifier(value="dog2")
    private Dog dog;
    
}
```

```xml
    <bean id="dog2" class="com.kuang.pojo.Dog"/>
    <bean id="people" class="com.kuang.pojo.People"/>
```

- **@Resource 和 @Autowired 的區別:**
  - **都是用來自動裝配的，都可以放在屬性**
  - **@Autowired 通過 byType 的方式實現，而且必須要求這個對象存在!【常用】**
  - **@Resource 默認通過 byName 的方式實現，如果找不到名字則通過 byType 實現! 如果兩個都找不到的情況下，就報錯!【常用】**
  - **執行順序: @Autowired 通過 byType，@Resource 默認通過 byName**

```java
public class People {
    @Resource(name = "cat1")
    private Cat cat;
    @Resource
    private Dog dog;
}
```

```xml
<bean id="cat" class="com.kuang.pojo.Cat"/>
<bean id="dog" class="com.kuang.pojo.Dog"/>
<bean id="people" class="com.kuang.pojo.People"/>
```

### 8-4-1、@Autowired<span id="s3"/>

- **修改 beans.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="cat" class="com.kuang.pojo.Cat"/>
    <bean id="dog" class="com.kuang.pojo.Dog"/>
    <bean id="people" class="com.kuang.pojo.People"/>

</beans>
```

- **修改 People.java** (@Autowired 也可以在 set 方式上使用，但配置上 Autowired 後就可以不用寫 set 了)

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class People {

    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public Dog getDog() {
        return dog;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

### 8-4-2、@Autowired(required = false) <span id="s4"/>

- **People.java 修改**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class People {

    // 如果顯示定義了 Autowired 的 required 屬性為 false，說明這個對象可以為 null，否則不允許為空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public Dog getDog() {
        return dog;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

- **beans2.xml 設定**

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

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="dog" class="com.kuang.pojo.Dog"/>
    <bean id="people" class="com.kuang.pojo.People"/>

</beans>
```

- **MyTest.java 修改**

```java
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.kuang.pojo.People;

public class MyTest {
    @Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");
        People people = context.getBean("people", People.class);
        people.getDog().shout();
        people.getCat().shout();
    }
}
```

- **測試結果**

```cs
People{cat=null, dog=com.kuang.pojo.Dog@64d2d351, name='null'}
```

### 8-5-3、@Autowired 配合 @Qualifier <span id="s5"/>

- **修改 beans.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="cat" class="com.kuang.pojo.Cat"/>
    <bean id="dog" class="com.kuang.pojo.Dog"/>
    <bean id="dog2" class="com.kuang.pojo.Dog"/>
    <bean id="people" class="com.kuang.pojo.People"/>

</beans>
```

- **People.java 修改**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class People {

    
    @Autowired
    private Cat cat;
    @Autowired
    @Qualifier(value="dog2")
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public Dog getDog() {
        return dog;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

### 8-5-4、@Nullable <span id="s6"/>

- **People.java，@Nullable 空值設定**

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.lang.Nullable;

public class People {

    private Cat cat;
    private Dog dog;
    private String name;
    
    // 表示可以空值
    public People(@Nullable String name) {
        this.name = name;
    }

    public Cat getCat() {
        return cat;
    }

    public Dog getDog() {
        return dog;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

- **可以空值所以不一定要設定 cat 與 dog 的 bean**

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

    <!-- 開啟註解的支持 -->
    <context:annotation-config/>

    <bean id="people" class="com.kuang.pojo.People"/>

</beans>
```

- **MyTest.java**

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");
    People people = context.getBean("people", People.class);
    System.out.println(people.toString());
}
```

- 執行結果

```cs
People{cat=null, dog=null, name='null'}

Process finished with exit code 0
```

### 8-5-5、@Resource <span id="s7"/>

- **修改 People.java**

```java
public class People {
    @Resource
    private Cat cat;
}
```

- **修改 bean.xml**

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
    <!-- 
        錯誤:
            1. 原因是因為有兩個 Cat (只能一個 Cat 類別，或符合規則 2)
            2. 宣告的 cat 無法與 bean 的 id 對應上 (private Cat cat 要等於 id="cat")
    -->
    <bean id="cat1" class="com.kuang.pojo.Cat"/> 
    <bean id="cat11" class="com.kuang.pojo.Cat"/>
    <bean id="people" class="com.kuang.pojo.People"/>

</beans>
```

### 8-5-6、註解實現自動裝配總結 <span id="s8"/>

- **project 之 pom.xml 的 常用依賴**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.6</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

- **註解說明**
  - **@Autowired: 自動裝配通過類型，名字**
    - **如果 Autowired 不能唯一自動配上屬性，則需要通過 @Qualifier(value="xxx") 去配合 @Autowired 的使用，指定一個唯一的 bean 對象注入 !**
  - **@Nullable: 字段標記了這個註解，說明這個字段可以為 null**
  - **@Resource: 自動裝配通過名字、類型**
  - **@Component: 組件，放在類上，說明這個類倍 Spring 管理了，就是 bean !**

---

- [【狂神說Java】12、自動裝配 Bean](https://www.bilibili.com/video/BV1WE411d7Dv?p=12&spm_id_from=pageDriver)
- [【狂神說Java】13、註解實現自動裝配](https://www.bilibili.com/video/BV1WE411d7Dv?p=13&spm_id_from=pageDriver)
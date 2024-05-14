[TOC]



# @SpringBootApplication

# @EnableAutoConfiguration

Spring Boot 将会自动配置应用程序的环境，包括自动检测并配置需要的 bean、组件和属性。这意味着，你不需要手动配置大量的 Spring Bean 和其他组件，Spring Boot 将会根据应用的依赖和配置来决定如何自动配置应用程序的运行环境。

总之，`@EnableAutoConfiguration` 的功能是根据应用的依赖和配置，自动配置 Spring Boot 应用程序的运行环境，让开发者可以更加专注于业务逻辑的开发，而不用花费过多的精力在配置上。

# @ImportResource

以前写的 springmvc.xml、applicationContext.xml 在 SpringBoot 里面并没有，我们自己编写的配置文件也不能自动识别，这里就可以使用 @ImportResource 标注在一个配置类上让 Spring 的配置文件生效。

```
注意！不使用 @ImportResource 注解，程序根本不能对我们 Spring 的配置文件进行加载
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/166b588979444dad84f4b0b9155700f5.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjQ3NzEx,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d8bb34eb73234fcdbed2cf2d38698791.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjQ3NzEx,size_16,color_FFFFFF,t_70)

```java
@ImportResource(locations = "classpath:applicationContext.xml")
@SpringBootApplication
@RestController
public class FirstSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(FirstSpringbootApplication.class, args);
    }
}

```

```java
@SpringBootTest
class FirstSpringbootApplicationTests {

    @Autowired
    ApplicationContext applicationContext;

    @Test
    void testapplication() {
        Object a = applicationContext.getBean("dog1");
        System.out.println(a);
    }
}

```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="dog1" class="com.yangzhenxu.firstspringboot.bean.Dog">
        <property name="name" value="zhangxue"/>
        <property name="age" value="27"/>
    </bean>
</beans>
```





### @Value

#### 两种注入方法

当你在 Spring 应用程序中使用 `@Value` 注解时，你可以将外部的值注入到 Spring Bean 中。这个注解有两种用法，分别使用 `${}` 和 `#{}`：

1. **@Value("${}")**： 这种用法允许你获取外部配置文件（比如 `application.properties` 或 `application.yml`）中定义的属性值。你可以在 `${}` 中指定属性的键，Spring 将会在配置文件中查找对应的键值对，并将其注入到标注了 `@Value` 注解的属性中。

   示例：假设在配置文件中有一行 `myapp.database.url=jdbc:mysql://localhost:3306/mydb`，你可以使用 `@Value` 注解来获取这个值：

   ```java
   @Value("${myapp.database.url}")
   private String databaseUrl;
   ```

   

2. **@Value("#{}")**： 这种用法允许你使用 SpEL（Spring Expression Language）表达式，通常用来获取其他 Bean 的属性或调用 Bean 的方法，并将结果注入到标注了 `@Value` 注解的属性中。

   示例：假设你有一个 `dataSource` Bean，你想要获取它的属性值：

   ```java
   @Value("#{dataSource.url}")
   private String dataSourceUrl;
   ```

总之，`@Value("${}")` 用来获取外部配置文件中的属性值，而 `@Value("#{}")` 用来使用 SpEL 表达式来动态获取其他 Bean 的属性或调用方法。

#### 使用方式

根据注入的内容来源，@ Value属性注入功能可以分为两种：**通过配置文件进行属性注入和通过非配置文件进行属性注入。**

**非配置文件注入的类型如下**：

1. 注入普通字符串
2. 注入操作系统属性
3. 注入表达式结果
4. 注入其他bean属性
5. 注入URL资源

基于配置文件的注入
首先，让我们看一下配置文件中的数据注入，无论它是默认加载的application.yml还是自定义my.yml文档（需要@PropertySource额外加载）。

application.yml文件配置，获得里面配置的端口号

![img](https://img-blog.csdnimg.cn/ec217e3925584315a79b6001f700368b.png)

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    /**
     *Get in application.yml
     */
    @Value("${server.port}")
    private String port;
 
 
    @Test
    public  void  getPort(){
 
       System.out.println(port);
    }
 
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516225446971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

自定义yml文件，application-config.yml文件配置，获得里面配置的用户密码值

**注意，如果想导入自定义的yml配置文件，应该首先把自定义文件在application.yml文件中进行注册，自定义的yml文件要以application开头，形式为application-fileName**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516231740942.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516232240507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    /**
     *Get in application-config.yml
     */
    @Value("${user.password}")
    private String password;
 
    @Test
    public  void  getPassword(){
 
       System.out.println(password);
    }
 
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516232457471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)



**基于配置文件一次注入多个值**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516233354545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
import java.util.List;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    /**
     *Injection array (automatically split according to ",")
     */
    @Value("${tools}")
    private String[] toolArray;
 
    /**
     *Injection list form (automatic segmentation based on "," and)
     */
    @Value("${tools}")
    private List<String> toolList;
 
    @Test
    public  void  getTools(){
 
       System.out.println(toolArray);
       System.out.println(toolList);
    }
 
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516233530597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

 **基于非配置文件的注入**

**在使用示例说明基于非配置文件注入属性的实例之前，让我们看一下SpEl。**

```
Spring Expression Language是Spring表达式语言，可以在运行时查询和操作数据。使用＃{…}作为操作符号，大括号中的所有字符均视为SpEl。
```

注入普通字符串

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
import java.util.List;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    // 直接将字符串赋值给 str 属性
    @Value("hello world")
    private String str;
 
 
    @Test
    public  void  getValue(){
 
        System.out.println(str);
    }
 
}
```

注入操作系统属性

**可以利用 @Value 注入操作系统属性**。

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    @Value("#{systemProperties['os.name']}")
    private String osName; // 结果：Windows 10
 
    @Test
    public  void  getValue(){
 
        System.out.println(osName);
    }
}
```

注入表达式结果

**在 @Value 中，允许我们使用表达式，然后自动计算表达式的结果。将结果复制给指定的变量。如下**

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    // 生成一个随机数
    @Value("#{ T(java.lang.Math).random() * 1000.0 }")
    private double randomNumber;
 
    @Test
    public  void  getValue(){
 
        System.out.println(randomNumber);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210516235225916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

**注入其他bean属性**

```java
package cn.wideth.controller;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
 
//其他bean，自定义名称为 myBeans
@Component("myBeans")
public class OtherBean {
 
    @Value("OtherBean的NAME属性")
    private String name;
 
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    @Value("#{myBeans.name}")
    private String fromAnotherBean;
 
    @Test
    public  void  getValue(){
 
        System.out.println(fromAnotherBean);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517000040873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)

注入URL资源

```java
package cn.wideth.controller;
 
import cn.wideth.PdaAndIpadApplication;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
import java.net.URL;
 
@RunWith(SpringRunner.class)
@SpringBootTest()
@ContextConfiguration(classes = PdaAndIpadApplication.class)
public class ValueController {
 
    /**
     *注入 URL 资源
     */
    @Value("https://www.baidu.com/")
    private URL homePage;
 
    @Test
    public  void  getValue(){
 
        System.out.println(homePage);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517000808324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70#pic_center)



# @ConfigurationProperties(prefix="person")

所谓“配置绑定”就是把配置文件中的值与 JavaBean 中对应的属性进行绑定。

通常，我们会把一些配置信息(例如，数据库配置)放在配置文件中，然后通过 Java 代码去读取该配置文件，并且把配置文件中指定的配置封装到 JavaBean(实体类) 中。


SpringBoot 提供了以下 2 种方式进行配置绑定：

使用 @ConfigurationProperties 注解
使用 @Value 注解
通过 Spring Boot 提供的 @ConfigurationProperties 注解，可以将全局配置文件中的配置数据绑定到 JavaBean 中。

下面我们以 Spring Boot 项目 helloworld 为例，演示如何通过 @ConfigurationProperties 注解进行配置绑定。

1. 在 helloworld 的全局配置文件 application.yml 中添加以下自定义属性。

  ```yaml
  person:
    lastName: 张三
    age: 18
    boss: false
    birth: 1990/12/12
    maps: { k1: v1,k2: 12 }
    lists:
      ‐ lisi
      ‐ zhaoliu
    dog:
      name: 迪迪
      age: 5
  
  
  ```

   2 在 helloworld 项目的 net.biancheng.www.bean 中创建一个名为 Person 的实体类，并将配置文件中的属性映射到这个实体类上，代码如下。

  ```java
  package net.biancheng.www.bean;
   
  import org.springframework.boot.context.properties.ConfigurationProperties;
  import org.springframework.stereotype.Component;
   
  import java.util.Date;
  import java.util.List;
  import java.util.Map;
   
  /**
  * 将配置文件中配置的每一个属性的值，映射到这个组件中
  *
  * @ConfigurationProperties：告诉 SpringBoot 将本类中的所有属性和配置文件中相关的配置进行绑定；
  * prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
  *
  * 只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能；
  */
  @Component
  @ConfigurationProperties(prefix = "person")
  public class Person {
      private String lastName;
      private Integer age;
      private Boolean boss;
      private Date birth;
      private Map<String, Object> maps;
      private List<Object> lists;
      private Dog dog;
   
      public Person() {
      }
   
      public String getLastName() {
          return lastName;
      }
   
      public void setLastName(String lastName) {
          this.lastName = lastName;
      }
   
      public Integer getAge() {
          return age;
      }
   
      public void setAge(Integer age) {
          this.age = age;
      }
   
      public Boolean getBoss() {
          return boss;
      }
   
      public void setBoss(Boolean boss) {
          this.boss = boss;
      }
   
      public Date getBirth() {
          return birth;
      }
   
      public void setBirth(Date birth) {
          this.birth = birth;
      }
   
      public Map<String, Object> getMaps() {
          return maps;
      }
   
      public void setMaps(Map<String, Object> maps) {
          this.maps = maps;
      }
   
      public List<Object> getLists() {
          return lists;
      }
   
      public void setLists(List<Object> lists) {
          this.lists = lists;
      }
   
      public Dog getDog() {
          return dog;
      }
   
      public void setDog(Dog dog) {
          this.dog = dog;
      }
   
      public Person(String lastName, Integer age, Boolean boss, Date birth, Map<String, Object> maps, List<Object> lists, Dog dog) {
          this.lastName = lastName;
          this.age = age;
          this.boss = boss;
          this.birth = birth;
          this.maps = maps;
          this.lists = lists;
          this.dog = dog;
      }
   
      @Override
      public String toString() {
          return "Person{" +
                  "lastName='" + lastName + '\'' +
                  ", age=" + age +
                  ", boss=" + boss +
                  ", birth=" + birth +
                  ", maps=" + maps +
                  ", lists=" + lists +
                  ", dog=" + dog +
                  '}';
      }
  }
  ```

  注意：

  只有在容器中的组件，才会拥有 SpringBoot 提供的强大功能。如果我们想要使用 @ConfigurationProperties 注解进行配置绑定，那么首先就要保证该对 JavaBean 对象在 IoC 容器中，所以需要用到 @Component 注解来添加组件到容器中。
  JavaBean 上使用了注解 @ConfigurationProperties(prefix = "person") ，它表示将这个 JavaBean 中的所有属性与配置文件中以“person”为前缀的配置进行绑定。

2.  在 net.biancheng.www.bean 中，创建一个名为 Dog 的 JavaBean，代码如下。

  ```java
  package net.biancheng.www.bean;
   
  public class Dog {
      private String name;
      private String age;
   
      public Dog() {
      }
   
      public Dog(String name, String age) {
          this.name = name;
          this.age = age;
      }
   
      public void setName(String name) {
          this.name = name;
      }
   
      public void setAge(String age) {
          this.age = age;
      }
   
      public String getName() {
          return name;
      }
   
      public String getAge() {
          return age;
      }
  }
  
  
  ```

  修改 HelloController 的代码，在浏览器中展示配置文件中各个属性值，代码如下。

```java
package net.biancheng.www.controller;
 
import net.biancheng.www.bean.Person;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
@Controller
public class HelloController {
    @Autowired
    private Person person;
 
    @ResponseBody
    @RequestMapping("/hello")
    public Person hello() {
        return person;
    }
}


```

4. 重启项目，使用浏览器访问 “http://localhost:8081/hello”，结果如下图。

![img](https://img-blog.csdnimg.cn/img_convert/e36dbb504277f648ecbfa5d919e5680c.png)



# @EnableConfigurationProperties



1. 结论
在@ConfigurationProperties的使用，把配置类的属性与yml配置文件绑定起来的时候，还需要加上@Component注解才能绑定并注入IOC容器中，若不加上@Component，则会无效。
@EnableConfigurationProperties的作用：则是将让使用了 @ConfigurationProperties 注解的配置类生效,将该类注入到 IOC 容器中,交由 IOC 容器进行管理，此时则不用再配置类上加上@Component。

2. 代码例子

1. @[ConfigurationProperties](https://so.csdn.net/so/search?q=ConfigurationProperties&spm=1001.2101.3001.7020)的使用

(提外话：具体的yml文件字符串、List、Map的书写方式并使用@ConfigurationProperties注入配置类.) [点这里](https://blog.csdn.net/xueyijin/article/details/123194585)
配置类

```java
@Component
@ConfigurationProperties(prefix = "demo")
@Data
public class DemoConfig {
    private String userName;
    private String age;
}

```

yml配置文件

```yaml
demo:
  user-name: hello
  age: 18

```

测试代码

```java
@Component
public class demo implements ApplicationRunner {

    @Autowired
    DemoConfig demoConfig;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(demoConfig);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b311e58576664e0c9473c8679bf9ee76.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pCPwrfmoqY=,size_20,color_FFFFFF,t_70,g_se,x_16)



2. @[EnableConfigurationProperties](https://so.csdn.net/so/search?q=EnableConfigurationProperties&spm=1001.2101.3001.7020)的使用

当去掉配置类的@Component时候，则会报下面错误提示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/a8b1482e1b8346ecacbcdd95825a8f06.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pCPwrfmoqY=,size_20,color_FFFFFF,t_70,g_se,x_16)

在测试代码上加上@EnableConfigurationProperties，参数指定那个配置类，该配置类上必须得有@ConfigurationProperties注解

```java
@Component
@EnableConfigurationProperties(DemoConfig.class)
public class demo implements ApplicationRunner {

    @Autowired
    DemoConfig demoConfig;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(demoConfig);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b2a6155c7b1c4bb8b2450f86243e6fca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pCPwrfmoqY=,size_20,color_FFFFFF,t_70,g_se,x_16)



3. 为什么会有@EnableConfigurationProperties出现呢？
有的人可能会问，直接在配置类上加@Component注解，不就可以了吗，为什么还要有@EnableConfigurationProperties出现呢？





----------------------------

第二篇详解

1. 引言
Spring Boot 提供了一种便捷的方式来管理和校验应用程序的配置，即通过类型安全的配置属性。@EnableConfigurationProperties 注解在这里扮演了重要的角色，它使得 Spring Boot 能够将外部配置文件中的属性绑定到强类型的 Java Beans 上。
![@EnableConfigurationProperties](https://img-blog.csdnimg.cn/direct/673c524eb46e44779fd488b1eb3a0051.png)

2. @EnableConfigurationProperties 的作用
@EnableConfigurationProperties 注解的主要作用是启用对 @ConfigurationProperties 注解类的支持。这包括：

将配置文件（如 application.properties 或 application.yml）中的属性绑定到带有 @ConfigurationProperties 注解的类上。
在需要时自动注册这些类为 Spring 上下文中的 Beans。
提供详尽的属性绑定结果（包括异常报告）。
通过使用 @EnableConfigurationProperties，开发者可以轻松实现外部配置的注入和管理，提高代码的模块化和可维护性。

3. 使用示例
下面通过一个示例来说明如何在 Spring Boot 3 应用中使用 @EnableConfigurationProperties。

假设你有一个配置属性类 AppProperties（可以是第三方包中的配置属性类，无法用@Component扫描）：

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private String description;

    // standard getters and setters
}

```

在这个类中，prefix = "app" 指定了属性名称的前缀。因此，name 和 description 字段将分别绑定到 app.name 和 app.description 配置属性上。

要启用这个配置属性类，你可以在任何配置类上使用 @EnableConfigurationProperties 注解：

```java
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

```

在这个例子中，@EnableConfigurationProperties(AppProperties.class) 注解使得 Spring Boot 自动将 application.properties 中的相关属性绑定到 AppProperties 类的实例上，并将该实例注册为 Spring 上下文的一个 Bean。

现在，你可以在应用中的任何位置注入 AppProperties：

```java
@Service
public class MyService {
    private final AppProperties appProperties;

    @Autowired
    public MyService(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    public void someMethod() {
        System.out.println("App name: " + appProperties.getName());
        // 使用 appProperties 的其他方法
    }
}

```

在这个服务类中，AppProperties 通过构造器注入，使得你可以在需要时使用配置属性。

4. 总结
通过使用 @EnableConfigurationProperties 注解，Spring Boot 应用可以非常方便地将外部配置映射到强类型的 Java Beans 上，从而使配置更加易于管理和维护。这种方法不仅提高了代码的清晰度和安全性，还降低了错误配置的风险。在 Spring Boot 3 中，这一机制仍然是管理和使用配置属性的推荐方式。





# @RestController

当使用Spring MVC构建RESTful风格的应用程序时，@RestController注解是一个非常实用的注解。它结合了@Controller和@ResponseBody两个注解的功能，并提供了更简洁的方式来编写处理HTTP请求并返回响应的控制器。

具体来说，@RestController注解用于标记一个类，表明该类是一个控制器，并且其下的方法都将返回数据作为响应。使用@RestController注解时，不再需要在方法上添加@ResponseBody注解，因为@RestController默认将所有方法的返回值自动序列化为响应体。

@RestController注解主要有以下特点和优势：

> 1：自动序列化：@RestController将控制器类中的方法的返回值自动序列化为适当的格式（如JSON、XML）作为响应体返回给客户端。
>
> 2：省略@ResponseBody注解：使用@RestController不需要在控制器方法上使用@ResponseBody注解，这减少了冗余的代码，使代码更加简洁。
>
> 3：结合@Controller和@ResponseBody：@RestController结合了@Controller和@ResponseBody注解的功能，既可以处理HTTP请求，又可以将方法的返回值直接序列化为响应数据。
>
> 4：常用于构建RESTful API：由于@RestController的灵活性和方便性，通常用于构建RESTful API，提供数据接口供客户端调用。

总之，@RestController注解简化了编写RESTful风格控制器的过程，使代码更加简洁和可读。它将控制器和方法的返回值自动序列化为响应体，方便开发者构建Web服务接口。



# @RequestMapping 配置url映射

@RequestMapping此注解即可以作用在控制器的某个方法上，也可以作用在此控制器类上。

当控制器在类级别上添加@RequestMapping注解时，这个注解会应用到控制器的所有处理器方法上。处理器方法上的@RequestMapping注解会对类级别上的@RequestMapping的声明进行补充。

看两个例子
**例子一：@RequestMapping仅作用在处理器方法上**

```java
@RestController
public class HelloController {

    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}
```

以上代码sayHello所响应的url=localhost:8080/hello。

**例子二：@RequestMapping仅作用在类级别上**

```java
/**
 * Created by wuranghao on 2017/4/7.
 */
@Controller
@RequestMapping("/hello")
public class HelloController {

    @RequestMapping(method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}
```

以上代码sayHello所响应的url=localhost:8080/hello,效果与例子一一样，没有改变任何功能。

**例子三：@RequestMapping作用在类级别和处理器方法上**

```java
/**
 * Created by wuranghao on 2017/4/7.
 */
@RestController
@RequestMapping("/hello")
public class HelloController {

    @RequestMapping(value="/sayHello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
    @RequestMapping(value="/sayHi",method= RequestMethod.GET)
    public String sayHi(){
        return "hi";
    }
}

```

这样，以上代码中的sayHello所响应的url=localhost:8080/hello/sayHello。

sayHi所响应的url=localhost:8080/hello/sayHi。



从这两个方法所响应的url可以回过头来看这两句话：当控制器在类级别上添加@RequestMapping注解时，这个注解会应用到控制器的所有处理器方法上。处理器方法上的@RequestMapping注解会对类级别上的@RequestMapping的声明进行补充。

最后说一点的是@RequestMapping中的method参数有很多中选择，一般使用get/post.

# @RequestParam

> localhost:8080/list1?userId=xxx 

加与不加的区别

```java
@RequestMapping("/list1")
public String test1(int userId) {
　　return "list";
}
@RequestMapping("/list2")
public String test2(@RequestParam int userId) {
　　return "list";
}

```

（1）不加@RequestParam前端的参数名需要和后端控制器的变量名保持一致才能生效

（2）不加@RequestParam参数为非必传，加@RequestParam写法参数为必传。但@RequestParam可以通过@RequestParam(required = false)设置为非必传。

（3）@RequestParam可以通过@RequestParam(“userId”)或者@RequestParam(value = “userId”)指定传入的参数名。（最主要的作用）

（4）@RequestParam可以通过@RequestParam(defaultValue = “0”)指定参数默认值

（5）如果接口除了前端调用还有后端RPC调用，则不能省略@RequestParam，否则RPC会找不到参数报错

（6）Get方式请求，参数放在url中时：

不加@RequestParam注解：url可带参数也可不带参数，输入 localhost:8080/list1 以及 localhost:8080/list1?userId=xxx 方法都能执行
加@RequestParam注解：url必须带有参数。也就是说你直接输入localhost:8080/list2 会报错，不会执行方法。只能输入localhost:8080/list2?userId=xxx 才能执行相应的方法

# @ResponseBody






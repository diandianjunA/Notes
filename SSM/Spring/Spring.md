# Spring

> Spring是最受欢迎的企业级Java应用程序开发框架，数以百万的来自世界各地的开发人员使用Spring框架来创建性能好、易于测试、可重用的代码。
>
> Spring框架是一个开源的Java平台，它最初是由Rod Johnson编写的，并且于2003年6月首次在Apache 2.0许可下发布。
>
> Spring是轻量级的框架，其基础版本只有2 MB左右的大小。
>
> Spring框架的核心特性是可以用于开发任何Java应用程序，但是在Java EE平台上构建web应用程序是需要扩展的。
>
> Spring框架的目标是使J2EE开发变得更容易使用，通过启用基于POJO编程模型来促进良好的编程实践。

## SpringFramework

Spring基础框架，可以视为Spring基础设施，基本上任何其他Spring项目都是以Spring Framework为基础的。

### SpringFramework的特性

- 非侵入式：使用Spring Framework开发应用程序时，Spring对应用程序本身的结构影响非常小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会破坏原有结构，反而能将组件结构进一步简化。这就使得基于Spring Framework开发应用程序时结构清晰、简洁优雅。
- 控制反转：IOC——Inversion of Control，翻转资源获取方向。把自己创建资源、向环境索取资源变成环境将资源准备好，我们享受资源注入。
- 面向切面编程：AOP——Aspect Oriented Programming，在不修改源代码的基础上增强代码功能。
- 容器：Spring IOC是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发效率。
- 组件化：Spring实现了使用简单的组件配置组合成一个复杂的应用。在Spring中可以使用XML和Java注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭建超大型复杂应用系统。
- 声明式：很多以前需要编写代码才能实现的功能，现在只需要声明需求即可由框架代为实现。
- 一站式：在IOC和AOP的基础上可以整合各种企业应用的开源框架和优秀的第三方类库。而且Spring旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在Spring Framework的基础上全部使用Spring来实现。

| 功能模块                    | 功能介绍                            |
| ----------------------- | ------------------------------- |
| Core Container          | 核心容器，在Spring环境下使用任何功能都必须基于IOC容器 |
| AOP&Aspects             | 面向切面编程                          |
| Testing                 | 提供了对junit或TestNG测试框架的整合         |
| Data Access/Integration | 提供了对数据访问/集成的功能                  |
| SpringMVC               | 提供了面向Web应用程序的集成功能               |

## IOC

### IOC思想

IOC:Inversion of Control，反转控制

**①获取资源的传统方式**

自己做饭：买菜、洗菜、择菜、改刀、炒菜，全过程参与，费时费力，必须清楚了解资源创建整个过程中的全部细节且熟练掌握。

在应用程序中的组件需要获取资源时，传统的方式是组件主动的从容器中获取所需要的资源，在这样的模式下开发人员往往需要知道在具体容器中特定资源的获取方式，增加了学习成本，同时降低了开发效率。

**②反转控制方式获取资源**

点外卖：下单、等、吃，省时省力，不必关心资源创建过程的所有细节。

反转控制的思想完全颠覆了应用程序组件获取资源的传统方式：反转了资源的获取方向——改由容器主动的将资源推送给需要的组件，开发人员不需要知道容器是如何创建资源对象的，只需要提供接收资源的方式即可，极大的降低了学习成本，提高了开发的效率。这种行为也称为查找的被动形式。

**③DI**

DI：Dependency Injection，翻译过来是依赖注入。

DI是IOC的另一种表述方式：即组件以一些预先定义好的方式（例如：setter方法）接受来自于容器的资源注入。相对于IOC而言，这种表述更直接。

所以结论是：IOC就是一种反转控制的思想，而DI是对IOC的一种具体实现。

### IOC在Spring中的实现

Spring的IOC容器就是IOC思想的一个落地的产品实现。IOC容器中管理的组件也叫做bean。在创建bean之前，首先需要创建IOC容器。Spring提供了IOC容器的两种实现方式：

①BeanFactory这是IOC容器的基本实现，是Spring内部使用的接口。面向Spring本身，不提供给开发人员使用。

②ApplicationContextBeanFactory的子接口，提供了更多高级特性。面向Spring的使用者，几乎所有场合都使用ApplicationContext而不是底层的BeanFactory。

### Spring依赖导入

```xml
<dependencies>
  <!--基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!--junit测试-->
  <dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

| 类型名                             | 简介                                       |
| ------------------------------- | ---------------------------------------- |
| ClassPathXmlApplicationContext  | 通过读取类路径下的XML格式的配置文件创建IOC容器对象             |
| FileSystemXmlApplicationContext | 通过文件系统路径读取XML格式的配置文件创建IOC容器对象            |
| ConfigurableApplicationContext  | ApplicationContext的子接口，包含一些扩展方法refresh()和close()，让ApplicationContext具有启动、关闭和刷新上下文的能力。 |
| WebApplicationContext           | 专门为Web应用准备，基于Web环境创建IOC容器对象，并将对象引入存入ServletContext域中。 |

### 基于XML管理bean

```java
public class HelloWorld {
    public void sayHello(){
        System.out.println("hello,spring");
    }
}
```

```xml
<!--
    bean:配置一个bean对象，将对象交给IOC容器管理
    id:bean的唯一标识
    class:设置bean对象所对应的类型
-->
<bean id="helloWorld" class="spring.pojo.HelloWorld"></bean>
```

```java
@Test
public void test(){
    //获取IOC容器
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    HelloWorld helloWorld = (HelloWorld) ioc.getBean("helloWorld");
    helloWorld.sayHello();
}
```

####获取bean的三种方式

1. 根据bean的id获取
2. 根据bean的类型获取(ioc容器中有且只有一个该类型的bean)
3. 根据id和类型共同获取

> 可以通过该类型实现的接口来获取，前提是ioc容器中只有一个该接口的实现类
>
> 同理，bean所继承的类也行

#### 依赖注入

相当于是为ioc容器中的对象的属性赋值

##### setter注入

```xml
<bean id="studentOne" class="spring.pojo.Student">
    <property name="sid" value="1001"></property>
    <property name="age" value="23"></property>
    <property name="gender" value="男"></property>
    <property name="sname" value="张三"></property>
</bean>
```

> 这是通过getXxx和setXxx来赋值的，和成员变量无关

##### 构造器注入

```xml
<bean id="studentTwo" class="spring.pojo.Student">
    <constructor-arg value="1002"></constructor-arg>
    <constructor-arg value="李四"></constructor-arg>
    <constructor-arg value="24"></constructor-arg>
    <constructor-arg value="女"></constructor-arg>
</bean>
```

> 这里的value是为构造器的参数赋值

为避免多个构造器参数类型一致但变量不一致，应在标签内加上name，即参数名

```xml
<bean id="studentTwo" class="spring.pojo.Student">
    <constructor-arg name="sid" value="1002"></constructor-arg>
    <constructor-arg name="sname" value="李四"></constructor-arg>
    <constructor-arg name="age" value="24"></constructor-arg>
    <constructor-arg name="gender" value="女"></constructor-arg>
</bean>
```

##### 特殊值处理

- 如果需要为某个属性赋值为null

```xml
<bean id="studentOne" class="spring.pojo.Student">
    <property name="sid" value="1001"></property>
    <property name="age" value="23"></property>
    <property name="gender">
        <null></null>
    </property>
    <property name="sname" value="张三"></property>
</bean>
```

- 如果赋值内容有特殊字符，需转换为其对应的实体

  ```xml
  < : &lt;
  > : &gt;
  ```

  或者使用CDATA节，内部的内容会原样解析

  ```xml
  <bean id="studentOne" class="spring.pojo.Student">
      <property name="sid" value="1001"></property>
      <property name="age" value="23"></property>
      <property name="gender">
          <value><![CDATA[<王五>]]></value>
      </property>
      <property name="sname" value="张三"></property>
  </bean>
  ```

##### 为类类型属性赋值

- ref赋值

  ```xml
  <bean id="studentThree" class="spring.pojo.Student">
      <property name="sid" value="1003"></property>
      <property name="sname" value="赵六"></property>
      <property name="age" value="25"></property>
      <property name="gender" value="男"></property>
      <property name="clazz" ref="clazzOne"></property>
  </bean>
  <bean id="clazzOne" class="spring.pojo.Clazz">
      <property name="cid" value="1111"></property>
      <property name="cname" value="嘴强王者班"></property>
  </bean>
  ```

> 通过当前文件内的bean的id来引用另一个对象，作为该对象的类类型属性的值

- 级联方式

```xml
<bean id="studentThree" class="spring.pojo.Student">
    <property name="sid" value="1003"></property>
    <property name="sname" value="赵六"></property>
    <property name="age" value="25"></property>
    <property name="gender" value="男"></property>
    <property name="clazz" value="clazzOne"></property>
    <property name="clazz.cid" value="2222"></property>
    <property name="clazz.cname" value="远大前程班"></property>
</bean>
<bean id="clazzOne" class="spring.pojo.Clazz">
    <property name="cid" value="1111"></property>
    <property name="cname" value="嘴强王者班"></property>
</bean>
```

> 前提是要有clazz对象，所以用的不多

- 内部bean

  ```xml
  <bean id="studentThree" class="spring.pojo.Student">
      <property name="sid" value="1003"></property>
      <property name="sname" value="赵六"></property>
      <property name="age" value="25"></property>
      <property name="gender" value="男"></property>
      <property name="clazz">
          <bean id="clazzInner" class="spring.pojo.Clazz">
              <property name="cid" value="1111"></property>
              <property name="cname" value="嘴强王者班"></property>
          </bean>
      </property>
  </bean>
  ```

  > 相当于在内部造了一个对象
  >
  > 这种情况这个bean不能被ioc容器获取

##### 为数组类型的属性赋值

```xml
<bean id="studentFour" class="spring.pojo.Student">
    <property name="sid" value="1003"></property>
    <property name="sname" value="赵六"></property>
    <property name="age" value="25"></property>
    <property name="gender" value="男"></property>
    <property name="clazz">
        <bean id="clazzInner" class="spring.pojo.Clazz">
            <property name="cid" value="1111"></property>
            <property name="cname" value="嘴强王者班"></property>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
            <!-- 字面量用value，类类型用ref
            <ref bean=""></ref>
            -->
        </array>
    </property>
</bean>
```

##### 为集合类型的属性赋值

- List集合

```xml
<bean id="clazzOne" class="spring.pojo.Clazz">
    <property name="cid" value="1111"></property>
    <property name="cname" value="嘴强王者班"></property>
    <property name="students">
        <list>
            <ref bean="studentOne"></ref>
            <ref bean="studentTwo"></ref>
            <ref bean="studentThree"></ref>
        </list>
    </property>
</bean>
```

或者使用util约束

```xml
<bean id="clazzOne" class="spring.pojo.Clazz">
    <property name="cid" value="1111"></property>
    <property name="cname" value="嘴强王者班"></property>
    <property name="students" ref="studentList"></property>
</bean>
<utils:list id="studentList">
    <ref bean="studentOne"></ref>
    <ref bean="studentTwo"></ref>
    <ref bean="studentThree"></ref>
</utils:list>
```

- Map集合

```xml
<bean id="studentFour" class="spring.pojo.Student">
    <property name="sid" value="1003"></property>
    <property name="sname" value="赵六"></property>
    <property name="age" value="25"></property>
    <property name="gender" value="男"></property>
    <property name="clazz">
        <bean id="clazzInner" class="spring.pojo.Clazz">
            <property name="cid" value="1111"></property>
            <property name="cname" value="嘴强王者班"></property>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
    <property name="teacherMap">
        <map>
            <entry key="10086" value-ref="teacherOne"></entry>
            <entry key="10080" value-ref="teacherTwo"></entry>
        </map>
    </property>
</bean>
<bean id="teacherOne" class="spring.pojo.Teacher">
    <property name="tid" value="10086"></property>
    <property name="tname" value="大宝"></property>
</bean>
<bean id="teacherTwo" class="spring.pojo.Teacher">
    <property name="tid" value="10080"></property>
    <property name="tname" value="小宝"></property>
</bean>
```

或者同样用utils

```xml
<bean id="studentFour" class="spring.pojo.Student">
    <property name="sid" value="1003"></property>
    <property name="sname" value="赵六"></property>
    <property name="age" value="25"></property>
    <property name="gender" value="男"></property>
    <property name="clazz">
        <bean id="clazzInner" class="spring.pojo.Clazz">
            <property name="cid" value="1111"></property>
            <property name="cname" value="嘴强王者班"></property>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
    <property name="teacherMap" ref="teacherMap"></property>
</bean>
<utils:map id="teacherMap">
    <entry key="10086" value-ref="teacherOne"></entry>
    <entry key="10080" value-ref="teacherTwo"></entry>
</utils:map>
<bean id="teacherOne" class="spring.pojo.Teacher">
    <property name="tid" value="10086"></property>
    <property name="tname" value="大宝"></property>
</bean>
<bean id="teacherTwo" class="spring.pojo.Teacher">
    <property name="tid" value="10080"></property>
    <property name="tname" value="小宝"></property>
</bean>
```

##### p命名空间

```xml
<bean id="studentFive" class="spring.pojo.Student" 
      p:sid="1005" p:sname="小明" p:teacherMap-ref="teacherMap"></bean>
```

##### 引入外部属性文件

加入mysql与druid的依赖

```xml
<!-- MySQL驱动 --> 
  <dependency> 
    <groupId>mysql</groupId> 
    <artifactId>mysql-connector-java</artifactId> 
    <version>8.0.16</version> </dependency> 
<!-- 数据源 -->
	<dependency> 
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId> 
      <version>1.0.31</version> 
	</dependency>
```

```xml
<!-- 引入外部属性文件 --> 
<context:property-placeholder location="classpath:jdbc.properties"/>
```

```xml
<!--在bean中配置-->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource"> 
  <property name="url" value="${jdbc.url}"/> 
  <property name="driverClassName" value="${jdbc.driver}"/> 
  <property name="username" value="${jdbc.user}"/> 
  <property name="password" value="${jdbc.password}"/> 
</bean>
```

#### bean的作用域

在Spring中可以通过配置bean标签的scope属性来指定bean的作用域范围，各取值含义参加下表：

| 取值            | 含义                      | 创建对象的时间   |
| ------------- | ----------------------- | --------- |
| singleton（默认） | 在IOC容器中，这个bean的对象始终为单实例 | IOC容器初始化时 |
| prototype     | 这个bean在IOC容器中有多个实例      | 获取bean时   |

如果是在WebApplicationContext环境下还会有另外两个作用域（但不常用）：

| 取值      | 含义         |
| ------- | ---------- |
| request | 在一个请求范围内有效 |
| session | 在一个会话范围内有效 |

#### bean的生命周期

```xml
<bean id="user" class="spring.pojo.User" init-method="initMethod" destroy-method="destroyMethod">
    <property name="id" value="1"/>
    <property name="username" value="admin"/>
    <property name="password" value="123456"/>
    <property name="age" value="23"/>
</bean>
```

- bean对象创建（调用无参构造器）
- 给bean对象设置属性
- bean对象初始化之前操作（由bean的后置处理器负责）
- bean对象初始化（需在配置bean时指定初始化方法）
- bean对象初始化之后操作（由bean的后置处理器负责）
- bean对象就绪可以使用
- bean对象销毁（需在配置bean时指定销毁方法）
- IOC容器关闭

scope属性：取值singleton（默认值），bean在IOC容器中只有一个实例，IOC容器初始化时创建对象

scope属性：取值prototype，bean在IOC容器中可以有多个实例，getBean()时创建对象

##### bean的后置处理器

bean的后置处理器会在生命周期的初始化前后添加额外的操作，需要实现BeanPostProcessor接口，且配置到IOC容器中，需要注意的是，bean后置处理器不是单独针对某一个bean生效，而是针对IOC容器中所有bean都会执行

创建bean的后置处理器：

```java
public class MyBeanProcessor implements BeanPostProcessor { 
  @Override 
  public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException { 
    System.out.println("☆☆☆" + beanName + " = " + bean); 
    return bean; 
  }
  @Override 
  public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException { 
    System.out.println("★★★" + beanName + " = " + bean); 
    return bean; 
  } 
}
```

在IOC容器中配置后置处理器：

```xml
<!-- bean的后置处理器要放入IOC容器才能生效 --> <bean id="myBeanProcessor" class="spring.process.MyBeanProcessor"/>
```

#### FactoryBean

FactoryBean是Spring提供的一种整合第三方框架的常用机制。和普通的bean不同，配置一个FactoryBean类型的bean，在获取bean的时候得到的并不是class属性中配置的这个类的对象，而是getObject()方法的返回值。通过这种机制，Spring可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。

将来我们整合Mybatis时，Spring就是通过FactoryBean机制来帮我们创建SqlSessionFactory对象的

创建类UserFactoryBean

```java
public class UserFactoryBean implements FactoryBean<User> { 
  @Override 
  public User getObject() throws Exception { 
    return new User(); 
  }
  @Override public Class<?> getObjectType() { 
    return User.class; 
  } 
}
```

配置bean

```xml
<bean id="user" class="bean.UserFactoryBean"></bean>
```

测试

```java
@Test public void testUserFactoryBean(){ //获取IOC容器 
  ApplicationContext ac = new ClassPathXmlApplicationContext("spring- factorybean.xml"); 
  User user = (User) ac.getBean("user"); 
  System.out.println(user); 
}
```

####基于xml的自动装配

> 自动装配：
>
> 根据指定的策略，在IOC容器中匹配某一个bean，自动为指定的bean中所依赖的类类型或接口类型属性赋值

使用bean标签的autowire属性设置自动装配效果

- 自动装配方式：byType

  byType：根据类型匹配IOC容器中的某个兼容类型的bean，为属性自动赋值

  若在IOC中，没有任何一个兼容类型的bean能够为属性赋值，则该属性不装配，即值为默认值null

  若在IOC中，有多个兼容类型的bean能够为属性赋值，则抛出异常NoUniqueBeanDefinitionException

  ```xml
  <bean id="userController" 
        class="autowire.xml.controller.UserController" 
        autowire="byType"> </bean> 
  <bean id="userService" 
        class="autowire.xml.service.impl.UserServiceImpl" 
        autowire="byType"> </bean> 
  <bean id="userDao" class="autowire.xml.dao.impl.UserDaoImpl"></bean>
  ```

- 自动装配方式：byName

  byName：将自动装配的属性的属性名，作为bean的id在IOC容器中匹配相对应的bean进行赋值

  ```xml
  <bean id="userController" 
        class="autowire.xml.controller.UserController" 
        autowire="byName"> </bean> 
  <bean id="userService" 
        class="autowire.xml.service.impl.UserServiceImpl" 
        autowire="byName"> </bean> 
  <bean id="userServiceImpl" 
        class=".xml.service.impl.UserServiceImpl" 
        autowire="byName"> </bean> 
  <bean id="userDao" class="autowire.xml.dao.impl.UserDaoImpl"></bean> 
  <bean id="userDaoImpl" 
        class="autowire.xml.dao.impl.UserDaoImpl"> </bean>
  ```

### 基于注解管理bean

常用注解

> @Component：将类标识为普通组件 
>
> @Controller：将类标识为控制层组件 
>
> @Service：将类标识为业务层组件 
>
> @Repository：将类标识为持久层组件

通过查看源码我们得知，@Controller、@Service、@Repository这三个注解只是在@Component注解的基础上起了三个新的名字。

对于Spring使用IOC容器管理这些组件来说没有区别。所以@Controller、@Service、@Repository这三个注解只是给开发人员看的，让我们能够便于分辨组件的作用。

注意：虽然它们本质上一样，但是为了代码的可读性，为了程序结构严谨我们肯定不能随便胡乱标记。

#### 扫描

情况一：最基本的扫描方式

```xml
<context:component-scan base-package="com.atguigu"> </context:component-scan>
```

情况二：指定要排除的组件

```xml
<context:component-scan base-package="com.atguigu"> 
  <!-- context:exclude-filter标签：指定排除规则 --> 
  <!--type：设置排除或包含的依据 
type="annotation"，根据注解排除，expression中设置要排除的注解的全类名 type="assignable"，根据类型排除，expression中设置要排除的类型的全类名 --> 
  <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/> 
  <!--<context:exclude-filter type="assignable" expression="com.atguigu.controller.UserController"/>-->
</context:component-scan>
```

情况三：仅扫描指定组件

```xml
<context:component-scan base-package="com.atguigu" use-default-filters="false"> 
  <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 --> 
  <!-- use-default-filters属性：取值false表示关闭默认扫描规则 --> 
  <!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 --> 
  <!--type：设置排除或包含的依据 
type="annotation"，根据注解排除，expression中设置要排除的注解的全类名 type="assignable"，根据类型排除，expression中设置要排除的类型的全类名 --> 
  <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/> 
  <!--<context:include-filter type="assignable" expression="com.atguigu.controller.UserController"/>--> 
</context:component-scan>
```

组件所对应的bean的id

在我们使用XML方式管理bean的时候，每个bean都有一个唯一标识，便于在其他地方引用。现在使用

注解后，每个组件仍然应该有一个唯一标识。

> 默认情况
>
> 类名首字母小写就是bean的id。例如：UserController类对应的bean的id就是userController。(小驼峰)
>
> 自定义bean的id
>
> 可通过标识组件的注解的value属性设置自定义的bean的id
>
> @Service("userService")//默认为userServiceImpl 
>
> public class UserServiceImpl implements UserService {
>
> }

#### 自动装配

- @Autowired注解

  在成员变量上直接标记@Autowired注解即可完成自动装配，不需要提供setXxx()方法。以后我们在项目中的正式用法就是这样。

  ```java
  @Controller 
  public class UserController { 
    @Autowired 
    private UserService userService; 
    public void saveUser(){ 
      userService.saveUser(); 
    } 
  }
  ```

  @Autowired注解可以标记在构造器和set方法上

  ```java
  @Controller 
  public class UserController { 
    private UserService userService;
    @Autowired 
    public UserController(UserService userService){ 
      this.userService = userService;
    }
    public void saveUser(){ 
      userService.saveUser();
    } 
  }
  ```

  ```java
  @Controller 
  public class UserController { 
    private UserService userService;
    @Autowired 
    public void setUserService(UserService userService){ 
      this.userService = userService; 
    }
    public void saveUser(){
      userService.saveUser(); 
    } 
  }
  ```

  @Autowired工作流程

  ![](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/Spring/Autowired.png)

  - 首先根据所需要的组件类型到IOC容器中查找

    - 能够找到唯一的bean：直接执行装配
    - 如果完全找不到匹配这个类型的bean：装配失败

    - 和所需类型匹配的bean不止一个

      - 没有@Qualifier注解：根据@Autowired标记位置成员变量的变量名作为bean的id进行匹配

        - 能够找到：执行装配
        - 找不到：装配失败
      - 使用@Qualifier注解：根据@Qualifier注解中指定的名称作为bean的id进行匹配
        - 能够找到：执行装配
        - 找不到：装配失败

  > @Autowired中有属性required，默认值为true，因此在自动装配无法找到相应的bean时，会报错装配失败
  >
  > 可以将属性required的值设置为true，则表示能装就装，装不上就不装，此时自动装配的属性为默认值
  >
  > 但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性。

## AOP

### 代理模式

二十三种设计模式中的一种，属于结构型模式。它的作用就是通过提供一个代理类，让我们在调用目标方法的时候，不再是直接对目标方法进行调用，而是通过代理类间接调用。让不属于目标方法核心逻辑的代码从目标方法中剥离出来——解耦。调用目标方法时先调用代理对象的方法，减少对目标方法的调用和打扰，同时让附加功能能够集中在一起也有利于统一维护。

- 代理：将非核心逻辑剥离出来以后，封装这些非核心逻辑的类、对象、方法。
- 目标：被代理“套用”了非核心逻辑代码的类、对象、方法。

#### 静态代理

```java
public class CalculatorStaticProxy implements Calculator { 
  // 将被代理的目标对象声明为成员变量 
  private Calculator target; 
  public CalculatorStaticProxy(Calculator target) { 
    this.target = target; 
  }
  @Override public int add(int i, int j) { 
    // 附加功能由代理类中的代理方法来实现 
    System.out.println("[日志] add 方法开始了，参数是：" + i + "," + j); 
    // 通过目标对象来实现核心业务逻辑 
    int addResult = target.add(i, j); 
    System.out.println("[日志] add 方法结束了，结果是：" + addResult);
    return addResult; 
  } 
}
```

> 静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。就拿日志功能来说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代码，日志功能还是分散的，没有统一管理。
>
> 提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理类来实现。这就需要使用动态代理技术了。

#### 动态代理

```java
public class ProxyFactory { 
  private Object target; 
  public ProxyFactory(Object target) { 
    this.target = target; 
  }
  public Object getProxy(){ 
    /**
    * newProxyInstance()：创建一个代理实例 
    * 其中有三个参数： 
    * 1、classLoader：加载动态生成的代理类的类加载器 
    * 2、interfaces：目标对象实现的所有接口的class对象所组成的数组 
    * 3、invocationHandler：设置代理对象实现目标对象方法的过程，即代理类中如何重写接 口中的抽象方法
    */ 
    ClassLoader classLoader = target.getClass().getClassLoader(); //应用类加载器
    Class<?>[] interfaces = target.getClass().getInterfaces(); 
    InvocationHandler invocationHandler = new InvocationHandler() { 
      @Override 
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        /**
        * proxy：代理对象 
        * method：代理对象需要实现的方法，即其中需要重写的方法 
        * args：method所对应方法的参数
        */ 
        Object result = null; 
        try {
          System.out.println("[动态代理][日志] "+method.getName()+"，参 数："+ Arrays.toString(args)); 
          result = method.invoke(target, args); 
          System.out.println("[动态代理][日志] "+method.getName()+"，结 果："+ result); 
        } catch (Exception e) { 
          e.printStackTrace(); 
          System.out.println("[动态代理][日志] "+method.getName()+"，异 常："+e.getMessage()); 
        } finally { 
          System.out.println("[动态代理][日志] "+method.getName()+"，方法 执行完毕"); 
        }
        return result; 
      } 
    };
    return Proxy.newProxyInstance(classLoader, interfaces, invocationHandler); 	} 
}
```

> 动态代理共有两种，以上为jdk动态代理
>
> 1. jdk动态代理：要求必须要有接口，最终生成的代理类在com.sun.proxy包下，与目标类实现相同的接口，类名为$proxy2
> 2. cglib动态代理，最终生成的代理类会继承目标类，并且与目标类在相同的包下

### AOP的概念与相关术语

AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况下给程序动态统一添加额外功能的一种技术。

- 横切关注点

  从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。

  这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横切关注点。

- 通知


  每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。

  - 前置通知：在被代理的目标方法前执行(@Before("pointCut()"))
  - 返回通知：在被代理的目标方法成功结束后执行（寿终正寝）(@AfterRunning(value="pointCut()",returning="result"))
  - 异常通知：在被代理的目标方法异常结束后执行（死于非命）(@AfterThrowing(value="pointCut()",throwing="ex"))
  - 后置通知：在被代理的目标方法最终结束后执行（盖棺定论）(@After("pointCut()"))
  - 环绕通知：使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置(@Around("pointCut()"))

- 切面

  封装通知方法的类。

- 目标


  被代理的目标对象。

- 代理

  向目标对象应用通知之后创建的代理对象。

- 连接点

  这也是一个纯逻辑概念，不是语法定义的。

  把方法排成一排，每一个横切位置看成x轴方向，把方法从上到下执行的顺序看成y轴，x轴和y轴的交叉点就是连接点。

- 切入点

  定位连接点的方式。

  每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说）。

  如果把连接点看作数据库中的记录，那么切入点就是查询记录的 SQL 语句。

  Spring 的 AOP 技术可以通过切入点定位到特定的连接点。

  切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。

> 作用：
>
> 简化代码：把方法中固定位置的重复的代码抽取出来，让被抽取的方法更专注于自己的核心功能，提高内聚性。
>
> 代码增强：把特定的功能封装到切面类中，看哪里有需要，就往上套，被套用了切面逻辑的方法就被切面给增强了。

### 基于注解的AOP

动态代理（InvocationHandler）：JDK原生的实现方式，需要被代理的目标类必须实现接口。因为这个技术要求代理对象和目标对象实现同样的接口（兄弟两个拜把子模式）。

cglib：通过继承被代理的目标类（认干爹模式）实现代理，所以不需要目标类实现接口。

AspectJ：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。weaver就是织入器。Spring只是借用了AspectJ中的注解。

在IOC所需依赖基础上再加入下面依赖即可：

```xml
<!-- spring-aspects会帮我们传递过来aspectjweaver -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>5.3.1</version>
</dependency>
```

创建切面类并配置

```xml
<!--基于注解的AOP的实现：
    1、将目标对象和切面交给IOC容器管理（注解+扫描）
    2、开启AspectJ的自动代理，为目标对象自动生成代理
    3、将切面类通过注解@Aspect标识
    -->
    <context:component-scan base-package="proxy"></context:component-scan>
    <!--开启基于注解的AOP-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

```java
@Component
@Aspect//标识为一个切面
public class LoggerAspect {
    //前置通知
    @Before("execution(public int proxy.CalculatorImpl.add(int,int))")
    public void beforeAdviceMethod(){
        System.out.println("前置通知");
    }
}
```

```java
@Test
public void TestAopByAnnotation(){
    ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("aop-annoation.xml");
    Calculator cal = ioc.getBean(Calculator.class);
    cal.add(1,2);
}
```

> 此处只能通过接口来获取对象，而且获取的还是一个代理对象

#### 切入点表达式语法

语法细节

- 用*号代替“权限修饰符”和“返回值”部分表示“权限修饰符”和“返回值”不限
- 在包名的部分，一个“\*”号只能代表包的层次结构中的一层，表示这一层是任意的。*
  - 例如：*.Hello匹配com.Hello，不匹配com.atguigu.Hello***
- *在包名的部分，使用“\*..”表示包名任意、包的层次深度任意
- 在类名的部分，类名部分整体用*号代替，表示类名任意
- 在类名的部分，可以使用*号代替类名的一部分*
  - *例如：\*Service匹配所有名称以Service结尾的类或接口
- 在方法名部分，可以使用*号表示方法名任意
- 在方法名部分，可以使用\*号代替方法名的一部分*
  - *例如：\*Operation匹配所有方法名以Operation结尾的方法
- 在方法参数列表部分，使用(..)表示参数列表任意
- 在方法参数列表部分，使用(int,..)表示参数列表以一个int类型的参数开头
- 在方法参数列表部分，基本数据类型和对应的包装类型是不一样的
  - 切入点表达式中使用 int 和实际方法中 Integer 是不匹配的
- 在方法返回值部分，如果想要明确指定一个返回值类型，那么必须同时写明权限修饰符
  - 例如：execution(public int ..Service.\*(.., int)) 正确
  - 例如：execution(* int ..Service.*(.., int)) 错误

![](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/Spring/CutPoint.png)

可简化

```java
"execution(* 包名.*.*(..))"
```

**PointCut与joinPoint**

- 声明

  公共的切入点表达式

```java
@Pointcut("execution(* com.atguigu.aop.annotation.*.*(..))") 
public void pointCut(){
  
}
```

- 在同一个切面中使用

```java
@Before("pointCut()") 
public void beforeMethod(JoinPoint joinPoint){ 
  String methodName = joinPoint.getSignature().getName();
  String args = Arrays.toString(joinPoint.getArgs()); 
  System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); 
}
```

- 在不同切面中使用

```java
@Before("com.atguigu.aop.CommonPointCut.pointCut()") public void beforeMethod(JoinPoint joinPoint){ 
  String methodName = joinPoint.getSignature().getName(); 
  String args = Arrays.toString(joinPoint.getArgs()); 
  System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); 
}
```

#### 获取通知的相应信息

- 获取连接点信息

  获取连接点信息可以在通知方法的参数位置设置JoinPoint类型的形参

  ```java
  @Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.*(..))") 
  public void beforeMethod(JoinPoint joinPoint){ 
    //获取连接点的签名信息 
    String methodName = joinPoint.getSignature().getName(); 
    //获取目标方法到的实参信息 
    String args = Arrays.toString(joinPoint.getArgs()); 
    System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); 
  }
  ```

- 获取目标方法的返回值

  @AfterReturning中的属性returning，用来将通知方法的某个形参，接收目标方法的返回值

  ```java
  @AfterReturning(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", returning = "result") 
  public void afterReturningMethod(JoinPoint joinPoint, Object result){ 
    String methodName = joinPoint.getSignature().getName(); 
    System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result); 
  }
  ```

- 获取目标方法的异常

  @AfterThrowing中的属性throwing，用来将通知方法的某个形参，接收目标方法的异常

  ```java
  @AfterThrowing(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", throwing = "ex") 
  public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){ 
    String methodName = joinPoint.getSignature().getName(); 
    System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex); 
  }
  ```

#### 环绕通知

环绕通知的返回值与目标方法的返回值应该一样

```java
@Around("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))") 
public Object aroundMethod(ProceedingJoinPoint joinPoint){ 
  String methodName = joinPoint.getSignature().getName(); 
  String args = Arrays.toString(joinPoint.getArgs()); 
  Object result = null; 
  try {
    System.out.println("环绕通知-->目标对象方法执行之前"); 
    //目标方法的执行，目标方法的返回值一定要返回给外界调用者 
    result = joinPoint.proceed(); 
    System.out.println("环绕通知-->目标对象方法返回值之后"); 
  } catch (Throwable throwable) { 
    throwable.printStackTrace(); 
    System.out.println("环绕通知-->目标对象方法出现异常时"); 
  } finally { 
    System.out.println("环绕通知-->目标对象方法执行完毕"); 
  }
  return result; 
}
```

#### 切面的优先级

相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序。

- 优先级高的切面：外面
- 优先级低的切面：里面

使用@Order注解可以控制切面的优先级：

- @Order(较小的数)：优先级高
- @Order(较大的数)：优先级低
- 默认值为Integer.MAX_VALUE

### 基于XML的AOP

```xml
<context:component-scan base-package="com.atguigu.aop.xml"></context:component- scan> 
<aop:config> 
  <!--配置切面类--> 
  <aop:aspect ref="loggerAspect"> 
    <aop:pointcut id="pointCut" expression="execution(* com.atguigu.aop.xml.CalculatorImpl.*(..))"/> 
    <aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before> 
    <aop:after method="afterMethod" pointcut-ref="pointCut"></aop:after> 
    <aop:after-returning method="afterReturningMethod" returning="result" pointcut-ref="pointCut"></aop:after-returning> 
    <aop:after-throwing method="afterThrowingMethod" throwing="ex" pointcut- ref="pointCut"></aop:after-throwing> 
    <aop:around method="aroundMethod" pointcut-ref="pointCut"></aop:around> </aop:aspect> 
  <aop:aspect ref="validateAspect" order="1"> 
    <aop:before method="validateBeforeMethod" pointcut-ref="pointCut"> 
    </aop:before> 
  </aop:aspect> 
</aop:config>
```

## 声明式事务

### jdbcTemplate

Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作

导入依赖

```xml
<dependencies>
  <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!-- Spring 持久化层支持jar包 -->
  <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个 jar包 -->
  <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!-- Spring 测试相关 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!-- junit测试 -->
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
  <!-- MySQL驱动 -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
  </dependency>
  <!-- 数据源 -->
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.31</version>
  </dependency>
</dependencies>
```

在ioc容器中配置jdbcTemplate

```xml
<!--引入jdbc.properties-->
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
<bean class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

此依赖为Spring整合Test的依赖，有了此依赖后，可通过依赖注入的方式在测试类中获取ioc容器中的bean

```xml
<!-- Spring 测试相关 -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-test</artifactId>
  <version>5.3.1</version>
</dependency>
```

```java
//指定当前测试类在Spring的测试环境中执行，此时可以通过注入的方式直接获取ioc容器中的bean
@RunWith(SpringJUnit4ClassRunner.class)
//设置Spring测试环境的配置文件
@ContextConfiguration("classpath:spring-jdbc.xml")
public class JdbcTemplateTest {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Test
    public void testInsert(){
        String sql="insert into t_user values(null,?,?,?,?,?)";
        jdbcTemplate.update(sql,"root","123",23,"女","123.qq.com");
    }
}
```

查询测试

```java
@Test
public void testGetUserById(){
    String sql="select * from t_user where id=?";
    User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), 1);
    System.out.println(user);
}
@Test
public void testGetAllUser(){
    String sql="select * from t_user";
    List<User> query = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(User.class));
    query.forEach(System.out::println);
}
@Test
public void testGetCount(){
    String sql="select count(*) from t_user";
    Integer integer = jdbcTemplate.queryForObject(sql, Integer.class);
    System.out.println(integer);
}
```

### 声明式事务概念

####编程式事务

事务功能的相关操作全部通过自己编写代码来实现

编程式的实现方式存在缺陷：

- 细节没有被屏蔽：具体操作过程中，所有细节都需要程序员自己来完成，比较繁琐。
- 代码复用性不高：如果没有有效抽取出来，每次实现功能都需要自己编写代码，代码就没有得到复用。

#### 声明式事务

既然事务控制的代码有规律可循，代码的结构基本是确定的，所以框架就可以将固定模式的代码抽取出来，进行相关的封装。

封装起来后，我们只需要在配置文件中进行简单的配置即可完成操作。

- 好处1：提高开发效率
- 好处2：消除了冗余的代码
- 好处3：框架会综合考虑相关领域中在实际开发环境下有可能遇到的各种问题，进行了健壮性、性能等各个方面的优化

所以，我们可以总结下面两个概念：

- 编程式：自己写代码实现功能
- 声明式：通过配置让框架实现功能

### 基于注解的声明式事务

新建表

```sql
CREATE TABLE `t_book` ( `book_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键', `book_name` varchar(20) DEFAULT NULL COMMENT '图书名称', `price` int(11) DEFAULT NULL COMMENT '价格', `stock` int(10) unsigned DEFAULT NULL COMMENT '库存（无符号）', PRIMARY KEY (`book_id`) ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8; 
insert into `t_book`(`book_id`,`book_name`,`price`,`stock`) values (1,'斗破苍 穹',80,100),(2,'斗罗大陆',50,100); 
CREATE TABLE `t_user` ( `user_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键', `username` varchar(20) DEFAULT NULL COMMENT '用户名', `balance` int(10) unsigned DEFAULT NULL COMMENT '余额（无符号）', PRIMARY KEY (`user_id`) ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8; 
insert into `t_user`(`user_id`,`username`,`balance`) values (1,'admin',50);
```

在spring的配置文件中配置事务管理器以及开启基于注解的事务管理

```xml
<!--配置事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!--
开启基于注解的事务管理
将使用@Transactional注解标识的方法或者类中的所有方法使用事务进行管理
-->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookDao bookDao;
    @Override
    @Transactional
    public void buyBook(Integer userId, Integer bookId) {
        //查询图书的价格
        Integer price=bookDao.getPriceByBookId(bookId);
        //更新图书的库存
        bookDao.updateStock(bookId);
        //更新用户的余额
        bookDao.updateBalance(userId,price);
    }
}
```

@Transactional注解标识的位置

- @Transactional标识在方法上，咋只会影响该方法
- @Transactional标识的类上，咋会影响类中所有的方法

####事务属性

- 只读

  对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这

  样数据库就能够针对查询操作来进行优化。

  ```java
  @Transactional(readOnly = true) 
  public void buyBook(Integer bookId, Integer userId) { 
    //查询图书的价格 
    Integer price = bookDao.getPriceByBookId(bookId); //更新图书的库存
    bookDao.updateStock(bookId); 
    //更新用户的余额 
    bookDao.updateBalance(userId, price); 
    //System.out.println(1/0); 
  }
  ```

  对增删改操作设置只读会抛出下面异常：

  Caused by: java.sql.SQLException: Connection is read-only. Queries leading to data modification

  are not allowed

- 超时

  事务在执行过程中，有可能因为遇到某些问题，导致程序卡住，从而长时间占用数据库资源。而长时间

  占用资源，大概率是因为程序运行出现了问题（可能是Java程序或MySQL数据库或网络连接等等）。

  此时这个很可能出问题的程序应该被回滚，撤销它已做的操作，事务结束，把资源让出来，让其他正常

  程序可以执行。

  概括来说就是一句话：超时回滚，释放资源

  ```java
  @Transactional(timeout = 3) 
  public void buyBook(Integer bookId, Integer userId) { 
    try {
      TimeUnit.SECONDS.sleep(5); 
    } catch (InterruptedException e) { 
      e.printStackTrace(); 
    }
    //查询图书的价格 
    Integer price = bookDao.getPriceByBookId(bookId); 
    //更新图书的库存 
    bookDao.updateStock(bookId); 
    //更新用户的余额 
    bookDao.updateBalance(userId, price);
    //System.out.println(1/0); 
  }
  ```

  执行过程中抛出异常：

  org.springframework.transaction.TransactionTimedOutException: Transaction timed out:

  deadline was Fri Jun 04 16:25:39 CST 2022

- 回滚策略

  声明式事务默认只针对运行时异常回滚，编译时异常不回滚。

  可以通过@Transactional中相关属性设置回滚策略

  - rollbackFor属性：需要设置一个Class类型的对象
  - rollbackForClassName属性：需要设置一个字符串类型的全类名
  - noRollbackFor属性：需要设置一个Class类型的对象
  - noRollbackForClassName属性：需要设置一个字符串类型的全类名

  ```java
  @Transactional(noRollbackFor = ArithmeticException.class) //@Transactional(noRollbackForClassName = "java.lang.ArithmeticException") 
  public void buyBook(Integer bookId, Integer userId) { 
    //查询图书的价格 
    Integer price = bookDao.getPriceByBookId(bookId); 
    //更新图书的库存 
    bookDao.updateStock(bookId); 
    //更新用户的余额
    bookDao.updateBalance(userId, price);
    System.out.println(1/0); 
  }
  ```

- 事务隔离级别


  数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响，避免各种并发问题。一个事

  务与其他事务隔离的程度称为隔离级别。SQL标准中规定了多种事务隔离级别，不同隔离级别对应不同

  的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。

  隔离级别一共有四种：

  - 读未提交：READ UNCOMMITTED

    允许Transaction01读取Transaction02未提交的修改。

  - 读已提交：READ COMMITTED、

    要求Transaction01只能读取Transaction02已提交的修改。

  - 可重复读：REPEATABLE READ

    确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01执行期间禁止其它事务对这个字段进行更新。

  - 串行化：SERIALIZABLE

    确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执行期间，禁止其它事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，但性能十分低下。

  各个隔离级别解决并发问题的能力见下表：

| 隔离级别             | 脏读   | 不可重复读 | 幻读   |
| ---------------- | ---- | ----- | ---- |
| READ UNCOMMITTED | 有    | 有     | 有    |
| READ COMMITTED   | 无    | 有     | 有    |
| REPEATABLE READ  | 无    | 无     | 有    |
| SERIALIZABLE     | 无    | 无     | 无    |

  > Oracle只支持 READ COMMITTED 和SERIALIZABLE这两种隔离级别，且默认隔离级别为 READ COMMITTED
  >
  > MySql支持四种隔离级别，且默认隔离级别为 REPEATABLE READ

  通过@Transaction来设置事务的隔离级别

  ```java
  @Transactional(isolation = Isolation.DEFAULT)//使用数据库默认的隔离级别 
  @Transactional(isolation = Isolation.READ_UNCOMMITTED)//读未提交 
  @Transactional(isolation = Isolation.READ_COMMITTED)//读已提交 
  @Transactional(isolation = Isolation.REPEATABLE_READ)//可重复读 
  @Transactional(isolation = Isolation.SERIALIZABLE)//串行化
  ```

- 事务的传播行为

  当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中

  运行，也可能开启一个新事务，并在自己的事务中运行。

  可以通过@Transactional中的propagation属性设置事务传播行为

  修改BookServiceImpl中buyBook()上，注解@Transactional的propagation属性

  - @Transactional(propagation = Propagation.REQUIRED)，默认情况，表示如果当前线程上有已经开启的事务可用，那么就在这个事务中运行。经过观察，购买图书的方法buyBook()在checkout()中被调用，checkout()上有事务注解，因此在此事务中执行。所购买的两本图书的价格为80和50，而用户的余额为100，因此在购买第二本图书时余额不足失败，导致整个checkout()回滚，即只要有一本书买不了，就都买不了
  - @Transactional(propagation = Propagation.REQUIRES_NEW)，表示不管当前线程上是否有已经开启的事务，都要开启新事务。同样的场景，每次购买图书都是在buyBook()的事务中执行，因此第一本图书购买成功，事务结束，第二本图书购买失败，只在第二次的buyBook()中回滚，购买第一本图书不受影响，即能买几本就买几本

### 基于XML的声明式事务

修改Spring配置文件

将Spring配置文件中去掉tx:annotation-driven 标签，并添加配置：

```xml
<aop:config> 
  <!-- 配置事务通知和切入点表达式 --> 
  <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.atguigu.spring.tx.xml.service.impl.*.*(..))">
  </aop:advisor> 
</aop:config> 
<!-- tx:advice标签：配置事务通知 --> 
<!-- id属性：给事务通知标签设置唯一标识，便于引用 --> 
<!-- transaction-manager属性：关联事务管理器 --> 
<tx:advice id="txAdvice" transaction-manager="transactionManager"> 
  <tx:attributes> 
    <!-- tx:method标签：配置具体的事务方法 --> 
    <!-- name属性：指定方法名，可以使用星号代表多个字符 --> 
    <tx:method name="get*" read-only="true"/> 
    <tx:method name="query*" read-only="true"/> 
    <tx:method name="find*" read-only="true"/> 
    <!-- read-only属性：设置只读属性 --> 
    <!-- rollback-for属性：设置回滚的异常 --> 
    <!-- no-rollback-for属性：设置不回滚的异常 --> 
    <!-- isolation属性：设置事务的隔离级别 --> 
    <!-- timeout属性：设置事务的超时属性 --> 
    <!-- propagation属性：设置事务的传播行为 --> 
    <tx:method name="save*" read-only="false" rollback- for="java.lang.Exception" propagation="REQUIRES_NEW"/> 
    <tx:method name="update*" read-only="false" rollback- for="java.lang.Exception" propagation="REQUIRES_NEW"/> 
    <tx:method name="delete*" read-only="false" rollback- for="java.lang.Exception" propagation="REQUIRES_NEW"/> 
  </tx:attributes> 
</tx:advice>
```

> 基于xml实现的声明式事务，必须引入aspectJ的依赖
>
> ```xml
> <dependency> 
>   <groupId>org.springframework</groupId> 
>   <artifactId>spring-aspects</artifactId> 
>   <version>5.3.1</version> 
> </dependency>
> ```


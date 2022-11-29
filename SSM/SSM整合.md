# SSM整合

## ContextLoaderListener

Spring提供了监听器ContextLoaderListener，实现ServletContextListener接口，可监听ServletContext的状态，在web服务器的启动，读取Spring的配置文件，创建Spring的IOC容器。web应用中必须在web.xml中配置

```xml
<listener> <!--配置Spring的监听器，在服务器启动时加载Spring的配置文件 Spring配置文件默认位置和名称：/WEB-INF/applicationContext.xml 可通过上下文参数自定义Spring配置文件的位置和名称 --> 
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class> 
</listener> <!--自定义Spring配置文件的位置和名称--> 
<context-param> 
  <param-name>contextConfigLocation</param-name> 
  <param-value>classpath:spring.xml</param-value> 
</context-param>
```

## 综合依赖

```xml
<packaging>war</packaging> 
<properties> 
  <spring.version>5.3.1</spring.version> 
</properties> 
<dependencies> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-context</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-beans</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <!--springmvc--> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-web</artifactId> 
    <version>${spring.version}</version> 
  </dependency>
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-webmvc</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-jdbc</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-aspects</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-test</artifactId> 
    <version>${spring.version}</version> 
  </dependency> 
  <!-- Mybatis核心 --> 
  <dependency> 
    <groupId>org.mybatis</groupId> 
    <artifactId>mybatis</artifactId> 
    <version>3.5.7</version> 
  </dependency> 
  <!--mybatis和spring的整合包--> 
  <dependency> 
    <groupId>org.mybatis</groupId> 
    <artifactId>mybatis-spring</artifactId> 
    <version>2.0.6</version> </dependency> 
  <!-- 连接池 --> 
  <dependency> 
    <groupId>com.alibaba</groupId> 
    <artifactId>druid</artifactId> 
    <version>1.0.9</version> 
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
  <!-- log4j日志 --> 
  <dependency> 
    <groupId>log4j</groupId> 
    <artifactId>log4j</artifactId> 
    <version>1.2.17</version> 
  </dependency> 
  <!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
  <dependency> 
    <groupId>com.github.pagehelper</groupId> 
    <artifactId>pagehelper</artifactId> 
    <version>5.2.0</version> 
  </dependency> 
  <!-- 日志 --> 
  <dependency> 
    <groupId>ch.qos.logback</groupId> 
    <artifactId>logback-classic</artifactId> 
    <version>1.2.3</version> 
  </dependency> 
  <!-- ServletAPI --> 
  <dependency> 
    <groupId>javax.servlet</groupId> 
    <artifactId>javax.servlet-api</artifactId> 
    <version>3.1.0</version> 
    <scope>provided</scope>
  </dependency> 
  <dependency> 
    <groupId>com.fasterxml.jackson.core</groupId> 
    <artifactId>jackson-databind</artifactId> 
    <version>2.12.1</version> 
  </dependency> 
  <dependency> 
    <groupId>commons-fileupload</groupId> 
    <artifactId>commons-fileupload</artifactId> 
    <version>1.3.1</version> 
  </dependency> 
  <!-- Spring5和Thymeleaf整合包 --> 
  <dependency> 
    <groupId>org.thymeleaf</groupId> 
    <artifactId>thymeleaf-spring5</artifactId> 
    <version>3.0.12.RELEASE</version> 
  </dependency> 
</dependencies>
```

创建表

```sql
CREATE TABLE `t_emp` ( 
  `emp_id` int(11) NOT NULL AUTO_INCREMENT, 
  `emp_name` varchar(20) DEFAULT NULL, 
  `age` int(11) DEFAULT NULL, 
  `sex` char(1) DEFAULT NULL, 
  `email` varchar(50) DEFAULT NULL, 
  PRIMARY KEY (`emp_id`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

## 配置web.xml

```xml
<!--配置Spring的编码过滤器-->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--配置将PUT,DELETE请求隐式转换为POST请求的过滤器-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--配置SpringMVC的中央调度控制器-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--设置自定义的SpringMVC的配置文件的位置和名称-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--将DispatcherServlet的初始化时间提前到服务器启动时-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--配置Spring的监听器，在服务器启动时加载Spring的配置文件-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--设置Spring的自定义的配置文件的位置与名称-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>
```

## 配置SpringMVC

```xml
<!--扫描控制层组件-->
<context:component-scan base-package="ssm.controller"/>
<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver"
      class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>
<!--配置默认的DispatcherServlet来处理静态资源-->
<mvc:default-servlet-handler/>
<!--开启mvc的注解驱动-->
<mvc:annotation-driven/>
<!--配置视图控制器-->
<mvc:view-controller path="/" view-name="index"/>
<!--配置文件上传解析器-->
<bean id="CommonsMultipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
```

## 配置Spring整合SpringMVC与MyBatis

```xml
<!--扫描组件，排除控制层-->
<context:component-scan base-package="ssm">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
<!--引入jdbc.properties文件-->
<context:property-placeholder location="classpath:jdbc.properties"/>
<!--配置数据源-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
<!--配置SqlSessionFactoryBean-->
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
    <!--设置mybatis的核心配置文件的路径-->
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <property name="dataSource" ref="dataSource"></property>
    <property name="typeAliasesPackage" value="ssm.pojo.Employee"></property>
    <!--设置映射文件的路径，只有当映射文件的包和mapper接口的包不一致时才需要设置-->
    <!-- <property name="mapperLocations" value="classpath:mappers/*.xml"></property>-->
    <!--配置分页插件-->
    <property name="plugins">
        <array>
            <bean class="com.github.pagehelper.PageInterceptor"></bean>
        </array>
    </property>
</bean>
<!--配置mapper接口扫描，可以将指定包下所有的mapper接口，通过上述配置的SqlSessionFactoryBean创建的SqlSession创建代理实现类对象，并且交给ioc容器管理-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="ssm.mapper"></property>
</bean>
```

## 创建日志文件log4j.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd"> 
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/"> 
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender"> 
        <param name="Encoding" value="UTF-8" /> 
        <layout class="org.apache.log4j.PatternLayout"> 
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" /> 
        </layout> </appender> <logger name="java.sql"> <level value="debug" /> 
</logger> <logger name="org.apache.ibatis"> 
    <level value="info" /> 
</logger> 
    <root>
        <appender-ref ref="STDOUT" /> 
    </root> 
</log4j:configuration>
```


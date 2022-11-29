# Mybatis

## mybatis核心配置文件

> 习惯上命名为mybatis-config.xml
>
> 主要用于配置连接数据库环境及mybatis的全局配置信息
>
> 存放位置为src/main/resource目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!--mybatis核心配置文件中的标签要按照顺序配置
(properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorFactory?,plugins?,environments?,databaseIdProvider?,mappers?）
    -->
  <!--引入jdbc配置文件，此后就可以在文件中使用${key}的方式访问value-->
    <properties resource="jdbc.properties"/>
  <!--取别名，将全类名简化-->
    <typeAliases>
        <typeAlias type="mybatis.pojo.User" alias="User"></typeAlias>
      	<!--指定包下所有的类都有默认别名-->
        <package name="mybatis.pojo"/>
    </typeAliases>
  <!--配置数据库环境-->
  <!--default:设置默认环境id-->
    <environments default="development">
        <environment id="development">
            <!--transaction:设置事务管理器
				属性：
					type：设置事务管理方式
					type="JDBC/MANAGED"
					JDBC:表示使用数据库原生的事务管理方式
					MANANGED:被管理，例如Spring
			-->
            <transactionManager type="JDBC"/>
            <!--dataSource:设置数据源类型
				属性：
					type="POOLED/UNPOOLED/JNDI"
					POOLED:表示使用数据库连接池
					UNPOOLED:不使用数据库连接池
					JNDI：表示使用上下文的数据源
			-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
   <!--引入mybatis的映射文件-->
    <mappers>
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
      <!--以包的方式引入映射文件，但必须满足两个条件
            1. mapper接口和映射文件所在的包必须一致
            2. mapper接口的名字与映射文件的名字保持一致
        -->
        <package name="mybatis.pojo.mapper"/>
    </mappers>
</configuration>
```

> driver:mysql8为：com.mysql.cj.jdbc.Driver
>
> url:jdbc:mysql://localhost:3306/数据库名?serverTimezone=UTC"
>
> username:root
>
> password:数据库密码

## 创建mapper接口

```java
package mybatis.pojo.mapper;

public interface UserMapper {
    int insertUser();
}
```

## 创建mybatis的映射文件

相关概念：ORM对象映射关系

- 对象：java实体类的对象
- 关系：关系型数据库
- 映射：二者的对应关系

| java | 数据库  |
| ---- | ---- |
| 类    | 表    |
| 属性   | 字段   |
| 对象   | 行    |

> 1. 映射文件命名规则：表所对应的类名+Mapper.xml
>
>    mybatis用于编写SQL，访问以及操作表中数据
>
>    存放位置为src/main/resources/mappers目录下
>
> 2. mapper接口与映射文件保证两个一致
>
>    - mapper接口的全类名和映射文件的namespace一致
>    - mapper接口中的方法名和映射文件中的sql的id保持一致

**不要忘记将映射文件的位置写到mybatis的核心配置文件中**

```xml
<!--映射文件内容-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="Mapper接口的位置">
  <!--编写sql语句-->
<insert id="insertUser">
        insert into t_user values(1,"mike","123456",23,"男","mike@gmail.com")
    </insert>
</mapper>
```

## MyBatis运行过程

```java
public class MyBatisTest {
    @Test
    public void testInsert() throws IOException {
        //获取核心配置文件的输入流
        InputStream myBatisConfigStream = Resources.getResourceAsStream("mybatis-config.xml");
        //获取SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        //获取SqlSessionBuilder对象
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(myBatisConfigStream);//根据核心配置文件的输入流来创建builder对象
        //获取sql的会话对象SqlSession，是mybatis提供的操作数据库的对象
        SqlSession sqlSession = sqlSessionFactory.openSession(boolean autoCommit);
        //获取UserMapper的代理类的实现对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        //调用mapper接口中的方法，实现添加用户的功能
        int result = mapper.insertUser();
        System.out.println("结果："+result);
        //提交事务
        sqlSession.commit();
        //关闭SqlSession对象
        sqlSession.close();
    }
}

```

## MyBatis映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mybatis.pojo.mapper.UserMapper">
    <insert id="insertUser">
        insert into t_user values(2,"mike","123456",23,"男","mike@gmail.com")
    </insert>
    <update id="updateUser">
        update t_user set username="peter",password="123789" where id=2
    </update>
    <delete id="deleteUser">
        delete from t_user where id=1;
    </delete>
    <!--User getUserById-->
    <!--
        resultType:设置结果类型，即查询的数据要转换的java类型
        resultMap:自定义映射，处理多对一或者一对多映射关系
    -->
    <select id="getUserById" resultType="mybatis.pojo.User">
        select * from t_user where id=1;
    </select>

    <select id="getAllUser" resultType="mybatis.pojo.User">
        select * from t_user;
    </select>
</mapper>
```

## 测试过程

```java
@Test
public void updateTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    mapper.updateUser();
    sqlSession.close();
}
@Test
public void deleteTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    mapper.deleteUser();
    sqlSession.close();
}
@Test
public void selectTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.getUserById();
    System.out.println(user);
    sqlSession.close();
}
@Test
public void getAllUserTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> users = mapper.getAllUser();
    users.forEach(System.out::println);
    sqlSession.close();
}
```
## mybatis获取参数

${}和#{}

- ${}本质上是字符串拼接，需要手动加单引号
- \#{}本质上是占位符赋值，无需手动加单引号

### 单个字面量类型的参数

```xml
<!--#{}的方式，无需加单引号-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mybatis.mapper.UserMapper">
    <select id="getUserByUserName" resultType="User">
        select * from t_user where username=#{username}
    </select>
</mapper>
```

```java
@Test
public void selectTest1(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.getUserByUserName("mike");
    System.out.println(user);
}
```

```xml
<!--${}的方式，要加单引号-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mybatis.mapper.UserMapper">
    <select id="getUserByUserName" resultType="User">
        select * from t_user where username='${username}'
    </select>
</mapper>
```

### 多个字面量类型的参数

```xml
<!--    <select id="checkLogin" resultType="User">-->
<!--        select * from t_user where username=#{arg0} and password=#{arg1}-->
<!--    </select>-->
<!--    或者-->
    <select id="checkLogin" resultType="User">
        select * from t_user where username=#{param1} and password=#{param2}
    </select>
```

> 当有多个参数时，mybatis会将参数放在map集合中，以两种方式存储数据
>
> 1. 以arg0,arg1...为键，以参数为值
> 2. 以param1,param2...为键，以参数为值

> 因此只需要通过#{}和${}来访问map集合的键即可获取相应的值
>
> ${}还是要注意加单引号

#### 自定义map键

```java
User checkLoginByMap(Map<String,Object> map);
```

```java
@Test
public void checkLoginByMapTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String,Object> map=new HashMap<>();
    map.put("username","mike");
    map.put("password","123456");
    User user = mapper.checkLoginByMap(map);
    System.out.println(user);
}
```

```xml
<select id="checkLoginByMap" resultType="User">
    select * from t_user where username=#{username} and password=#{password}
</select>
```

若mapper接口方法的参数为实体类类型的参数

可通过#{},${}访问实体类中的属性名，获取相应的属性值，还是要注意\${}的单引号问题

```xml
<insert id="insertUser">
    insert into t_user values(null,#{username},#{password},#{age},#{gender},#{email})
</insert>
```

```java
@Test
public void insertUserTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = new User(3, "helen", "000823", 21, "女", "helen@gmail.com");
    mapper.insertUser(user);
}
```

#### 使用@Param注解设置map键值对

```java
User checkLoginByParam(@Param(value="username") String username, @Param(value = "password") String password);
```

```xml
<select id="checkLoginByParam" resultType="User">
    select * from t_user where username=#{username} and password=#{password}
</select>
```

### 当用map作为返回类型的时候

查询一条数据，将得到一个map，里面的数据将是以字段名为键，以该条数据的该字段值为值的多个键值对

```java
/**
 * 查询出一条用户信息，用map接收
 * @return
 */
Map<String,Object> getUserByIdAsMap(@Param(value = "id") int id);
```

```xml
<select id="getUserByIdAsMap" resultType="map">
    select * from t_user where id=#{id};
</select>
```

```java
@Test
public void getUserByIdAsMapTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> userMap = mapper.getUserByIdAsMap(2);
    System.out.println(userMap);
}
```

![Map返回值](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/MyBatis/Map.png)

> 如果某个字段值为null，则不会放在这个map集合里面

查询多条数据时，返回值用List包住，将获得多个map，存在一个list中，每个map代表一条数据

```java
/**
 * 查询所有的用户信息为map集合放在List中
 * @return
 */
List<Map<String,Object>> getAllUserAsMap();
```

```java
@Test
public void getAllUserAsMapTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<Map<String, Object>> allUserMapList = mapper.getAllUserAsMap();
    allUserMapList.forEach(System.out::println);
}
```

![ListMap返回值](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/MyBatis/ListMap.png)

也可直接用map接收，但是要用注解@MapKey来指定键为哪个字段，指定后，通过该字段的值即可找到该条数据，该数据依然为一个map，作为外围map的值存储在外围map中

```java
@MapKey("id")
Map<String,Object> getAllUserToMap();
```

```java
@Test
public void getAllUserToMapTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> allUserToMap = mapper.getAllUserToMap();
    for (Map.Entry<String, Object> stringObjectEntry : allUserToMap.entrySet()) {
        System.out.println(stringObjectEntry);
    }
}
```

![Map注解返回值](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/MyBatis/MapAnnotation.png)

## 特殊的SQL执行

### 模糊查询

解决方法

1. 用${xxx}(此处不能用#{})，因为\${xxx}本质是字符串拼接

```xml
<select id="getUserByLike" resultType="User">
    select * from t_user where username like '%${mohu}%'
</select>
```

2. 使用SQL中的字符串拼接函数concat

```xml
<select id="getUserByLike" resultType="User">
    select * from t_user where username like concat('%',#{mohu},'%')
</select>
```

### 批量删除

```xml
<delete id="deleteMoreUser">
    delete from t_user where id in(${ids})
</delete>
```

### 动态设置表名

表名不能加单引号，所以只能用${xxx}

```xml
<select id="getUserList" resultType="User">
    select * from ${tableName}
</select>
```

### 添加功能获取自增的主键

```xml
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null,#{username},#{password},#{age},#{gender},#{email})
</insert>
```

useGeneratedKeys:表明允许获取自增的主键

keyProperty:表明将获取的自增的主键放在你所插入的对象的哪个属性内

## 搭建Mybatis框架

### 自定义映射resultMap

#### 字段名和属性名不一致

1. 在SQL语句中起别名

```xml
<select id="getEmpById" resultType="mybatis.pojo.Emp">
    select emp_id empId,emp_name empName,age,gender from t_emp where emp_id=#{empId}
</select>
```

2. 在核心配置文件中设置将下划线型名映射为驼峰型名

   将以下这段代码加到properties标签下方

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

3. 用resultMap来自定义映射

```xml
<resultMap id="empResultMap" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
</resultMap>
<select id="getEmpById" resultMap="empResultMap">
    select * from t_emp where emp_id=#{empId}
</select>
```

> resultMap:设置自定义的映射关系
>
> - id:唯一标识(主键)
> - type:处理映射关系的实体类类型
>
> id:处理主键和实体类中属性的映射关系
>
> result:处理普通字段与实体类中属性的映射关系
>
> column:设置映射关系中MySQL表内的字段名
>
> property:设置映射关系中实体类中的属性名

### 多对一关系处理

####级联方式

```xml
<resultMap id="empAndDeptResult" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
    <result column="dept_id" property="dept.deptId"></result>
    <result column="dept_name" property="dept.deptName"></result>
</resultMap>
<select id="getEmpAndDept" resultMap="empAndDeptResult">
    select t_emp.*,t_dept.*
    from t_emp
        left join t_dept
            on t_emp.dept_id=t_dept.dept_id
    where t_emp.emp_id=#{empId}
</select>
```

> 当实体类的属性中存在另一个实体类且有多个属性时，即为多对一情况，此时仅需在property中写明该字段与该实体类属性对象中的哪个属性对应即可

#### association

asscoiation:处理多对一的映射关系(处理实体类类型的属性)

```xml
<resultMap id="empAndDeptResult" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
    <association property="dept" javaType="Dept">
        <id column="dept_id" property="deptId"></id>
        <result column="dept_name" property="deptName"></result>
    </association>
</resultMap>
```

#### 分步查询

```xml
<resultMap id="empAndDeptByStepOne" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
    <association property="dept"
                 select="mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                 column="dept_id"></association>
</resultMap>
<select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepOne">
    select * from t_emp where emp_id=#{empId}
</select>
```

```xml
<select id="getEmpAndDeptByStepTwo" resultType="Dept">
    select * from t_dept where dept_id=#{deptId}
</select>
```

> 相当于余下的未被匹配的属性和字段，到另一个表去进行第二次查询，查询得出的结果放入到那个实体类属性中，值得注意的是，**在第二个mapper.xml文件中如果没有进行别名处理的话依然无法查询得到正确的结果**

##### 延时加载

> lazyLoadingEnabled:延迟加载的全局开关，当开启时，所有的关联对象都会延迟加载
>
> aggressiveLazyLoading:当开启时，任何方法的调用都会加载该对象的所有属性，否则，每个属性都会按需加载

```xml
<!--开启延迟加载-->
<setting name="lazyLoadingEnabled" value="true"/>
<setting name="aggressiveLazyLoading" value="true"/>
```

> 放在\<settings\>标签里

此时如果你只使用了Emp内的普通属性(非实体类对象属性)，此时只会加载第一个查询，不会有第二个查询

当aggressiveLazyLoading设置为false时，lazyLoadingEnabled才会有效果

- 如果想让单独某个分步查询进行/不进行延时加载，可在association标签内加上fatchType

```xml
<resultMap id="empAndDeptByStepOne" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
    <association property="dept" fetchType="eager"
                 select="mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                 column="dept_id"></association>
</resultMap>
```

eager:不进行延时加载

lazy:进行延时加载

### 一对多关系处理

#### collection

> collection:处理一对多的映射关系(处理集合类型的属性)

```xml
<resultMap id="deptAndEmpResult" type="Dept">
    <id column="dept_id" property="deptId"></id>
    <result column="dept_name" property="deptName"></result>
    <collection property="emps" ofType="Emp">
        <id column="emp_id" property="empId"></id>
        <result column="emp_name" property="empName"></result>
        <result column="age" property="age"></result>
        <result column="gender" property="gender"></result>
    </collection>
</resultMap>
<select id="getDeptAndEmpByDeptId" resultMap="deptAndEmpResult">
    select * from t_dept left join t_emp on t_dept.dept_id=t_emp.dept_id where t_dept.dept_id=#{deptId}
</select>
```

#### 分步查询

```xml
<resultMap id="DeptAndEmpByStep" type="Dept">
    <id column="dept_id" property="deptId"></id>
    <result column="dept_name" property="deptName"></result>
    <collection property="emps"
                select="mybatis.mapper.EmpMapper.getDeptAndEmpByStepTwo"
                column="dept_id"></collection>
</resultMap>
<select id="getDeptAndEmpByStepOne" resultMap="DeptAndEmpByStep">
    select * from t_dept where dept_id=#{deptId}
</select>
```

```xml
<select id="getDeptAndEmpByStepTwo" resultType="Emp">
    select * from t_emp where dept_id=#{deptId}
</select>
```

通过第一张表查询的字段，作为查询第二张表的条件，将第二张表查询的结果作为值赋给第一张表中的实体类对象(集合对象)，这就是分步查询的实质

## 动态SQL

### if标签

```xml
<mapper namespace="mybatis.mapper.DynamicSqlMapper">
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp where 1=1
            <if test="empName!=null and empName !=''">
                and emp_name=#{empName}
            </if>
            <if test="age!=null and age!=''">
                and age=#{age}
            </if>
            <if test="gender!=null and gender!=''">
                and gender=#{gender}
            </if>
    </select>
</mapper>
```

> 根据标签中的test属性中的表达式判断标签中的内容是否有效，有效即可拼接到SQL语句中

或者如下

###where标签

```xml
<mapper namespace="mybatis.mapper.DynamicSqlMapper">
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp
        <where>
            <if test="empName!=null and empName !=''">
                and emp_name=#{empName}
            </if>
            <if test="age!=null and age!=''">
                and age=#{age}
            </if>
            <if test="gender!=null and gender!=''">
                and gender=#{gender}
            </if>
        </where>
    </select>
</mapper>
```

> where标签的三个作用
>
> 1. 若where标签中有if条件成立，会自动生成where关键字
> 2. 会自动将where标签中内容前多余的and去掉，但是内容后多余的and无法取出
> 3. 若where标签中没有任何条件成立，则where标签没有任何功能

### trim标签

```xml
<trim prefix="where" suffixOverrides="and">
    <if test="empName!=null and empName !=''">
        emp_name=#{empName} and
    </if>
    <if test="age!=null and age!=''">
        age=#{age} and
    </if>
    <if test="gender!=null and gender!=''">
        gender=#{gender} and
    </if>
</trim>
```

> prefix,suffix:在标签中内容前面或后面添加指定内容
>
> prifixOverride,suffixOverride:在标签中内容前面或后面去除指定内容

### choose,when,otherwise标签

```xml
<select id="getEmpByChoose" resultType="Emp">
    select * from t_emp
    <where>
        <choose>
            <when test="empName!=null and empName!=''">
                emp_name=#{empName}
            </when>
            <when test="age!=null and age empName!=''">
                age=#{age}
            </when>
            <when test="gender!=null and gender!=''">
                gender=#{gender}
            </when>
        </choose>
    </where>
</select>
```

相当于if...else if....else if.....else

when相当于else if，至少有一个，otherwise相当于else，至多有一个

### foreach标签

```xml
<insert id="insertMoreEmp">
    insert into t_emp values
    <foreach collection="emps" item="emp" separator=",">
        (null,#{emp.empName},#{emp.age},#{emp.gender},null)
    </foreach>
</insert>
```

> 相当于循环，以上为以集合来批量添加

```xml
<delete id="deleteMoreEmp">
    delete from t_emp where emp_id in
    (
        <foreach collection="empIds" item="empId" separator=",">
            #{empId}
        </foreach>
        )
</delete>
```

> 这是通过数组来循环

foreach的属性：

1. collection:要循环的集合或者数组
2. item:表示集合或者数组中的一个元素
3. separator:用什么来间隔
4. index:循环的索引
5. open:以什么开始
6. close:以什么结束

或者用以下方式批量删除：...or...or...

```xml
<delete id="deleteMoreEmp">
    delete from t_emp where
    <foreach collection="empIds" item="empId" separator="or">
        emp_id=#{empId}
    </foreach>
</delete>
```

### sql标签

```xml
<sql id="empColumns">
    emp_id,emp_name,age,gender,dept_id
</sql>
<select id="getEmpByCondition" resultType="Emp">
    select <include refid="empColumns"></include> from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="empName!=null and empName !=''">
            emp_name=#{empName} and
        </if>
        <if test="age!=null and age!=''">
            age=#{age} and
        </if>
        <if test="gender!=null and gender!=''">
            gender=#{gender} and
        </if>
    </trim>
</select>
```

> 将一些内容写在标签内，在其他标签里可通过include标签来引用，记录一段SQL片段

 ## MyBatis的缓存

### MyBatis的一级缓存

是SqlSession级别的，同一个SqlSession反复查询同一条数据时即可直接从缓存中获取

但当两次查询之间执行了任意一次增删改，会清空缓存(因为增删改会改变数据库的数据，缓存就不一定管用了，所以清空)

```java
sqlSession.clearCache();
```

sqlSession清空缓存的方法

### MyBatis的二级缓存

是SqlSessionFactory级别的

1. 需要在全局配置文件中进行配置cacheEnabled="true"
2. 在映射文件中添加标签\<cache/>
3. 在SqlSession关闭或提交后才会保存到二级缓存中(否则保存在一级缓存中)
4. 查询的数据的实体类必须实现序列化接口

两次查询之间执行了任意一次增删改，也会清空二级缓存

#### 二级缓存中的相关配置

在mapper配置文件中添加的cache标签可以设置一些属性：

①eviction属性：缓存回收策略，默认的是LRU。

1. LRU（Least Recently Used）–最近最少使用的：移除最长时间不被使用的对象。
2. FIFO（First in First out）–先进先出：按对象进入缓存的顺序来移除它们。
3. SOFT –软引用：移除基于垃圾回收器状态和软引用规则的对象。
4. WEAK –弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。

②flushInterval属性：刷新间隔，单位毫秒默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新

③size属性：引用数目，正整数代表缓存最多可以存储多少个对象，太大容易导致内存溢出

④readOnly属性：只读，true/false

- true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。
- false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是false。

### 缓存查询顺序

先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。

如果二级缓存没有命中，再查询一级缓存

如果一级缓存也没有命中，则查询数据库

SqlSession关闭之后，一级缓存中的数据会写入二级缓存

## Mybatis逆向工程

### pom.xml文件中的依赖与插件

```xml
<!--依赖MyBatis核心包-->
<dependencies>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
  </dependency>
  <!--junit测试-->
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
  <!--log4j日志-->
  <dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
  </dependency>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
  </dependency>
</dependencies>
<!--控制Maven在构建过程中相关配置-->
<build>
<!--构建过程中用到的插件-->
<plugins>
  <!--具体插件，逆向工程的操作是以构建过程中插件形式出现的-->
  <plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.0</version>
    <!--插件的依赖-->
    <dependencies>
      <!--逆向工程的核心依赖-->
      <dependency>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-core</artifactId>
        <version>1.3.2</version>
      </dependency>
      <!--MySQL驱动-->
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
      </dependency>
    </dependencies>
  </plugin>
</plugins>
</build>
```

### 逆向工程核心配置文件(generatorConfig.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--targetRuntime:执行生成的逆向工程的版本
    MyBatis3Simple:生成基本的CRUD清新简洁版）
    MyBatis3:生成带条件的CRUD（奢华尊享版）-->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--数据库的连接信息-->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                userId="root"
                password="Xjs02220012">
        </jdbcConnection>
        <!--javaBean的生成策略-->
        <javaModelGenerator
                targetPackage="mybatis.pojo"
                targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--SQL映射文件的生成策略-->
        <sqlMapGenerator
                targetPackage="mybatis.mapper"
                targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!--Mapper接口的生成策略-->
        <javaClientGenerator
                type="XMLMAPPER"
                targetPackage="mybatis.mapper"
                targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!--逆向分析的表-->
        <!--tableName设置为*号，可以对应所有表，此时不写domainObjectName-->
        <!--domainObjectName属性指定生成出来的实体类的类名-->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```

### 逆向工程生成类与mapper的使用

```java
/**
 * 根据条件查询数据
 */
@Test
public void mbgTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    EmpExample empExample = new EmpExample();
    empExample.createCriteria().andEmpNameEqualTo("张三");
    empExample.or().andGenderEqualTo("男");
    for (Emp emp : mapper.selectByExample(empExample)) {
        System.out.println(emp);
    }
}
```

```java
/**
     * 普通修改功能与选择性修改
     */
    @Test
    public void modifyTest(){
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        Emp emp = new Emp(1, "小黑", null, "女");
//        mapper.updateByPrimaryKey(emp);普通修改
        mapper.updateByPrimaryKeySelective(emp);
    }
```

> 选择性修改时，如果传入实体类属性为null，则不会修改该数属性对应的字段

## 分页插件

###分页功能分析

limit index,pageSize

index:当前页的起始索引,index=(pageNum-1)*pageSize

pageSize:每页显示条数

pageNum:当前页的页码

count:总记录数

totalPage:totalPage=(count+pageSize-1)/pageSize

limit (pageNum-1)*pageSize,pageSize

<a>首页</a> <a>上一页</a> 2 3 4 5 6 <a>下一页</a> <a>尾页</a>

### 分页插件使用

pom.xml中导入依赖

```xml
<dependency>
  <groupId>com.github.pagehelper</groupId>
  <artifactId>pagehelper</artifactId>
  <version>5.2.0</version>
</dependency>
```

mybatis核心配置文件中加入plugin

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

```java
@Test
public void pageTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    //查询功能前开启分页功能(相当于一个拦截器，来拦截查询语句)
    PageHelper.startPage(1,4);
    List<Emp> emps = mapper.selectByExample(null);
    emps.forEach(System.out::println);
}
```

效果如下

![分页效果](http://www.diandianjun.com.cn:8080/resource/blog/text/SSM/MyBatis/paging.png)

### 通过分页插件获取与分页相关的数据

```java
@Test
public void pageTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    //查询功能前开启分页功能
    Page<Object> page = PageHelper.startPage(1, 4);
    List<Emp> emps = mapper.selectByExample(null);
    emps.forEach(System.out::println);
    System.out.println(page);
}
```

可获取对象page，page内存放数据：

Page{count=true, pageNum=1, pageSize=4, startRow=0, endRow=4, total=30, pages=8, reasonable=false, pageSizeZero=false}以及查询到的实体类对象

或者使用pageInfo

```java
@Test
public void pageTest(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    //查询功能前开启分页功能
    Page<Object> page = PageHelper.startPage(1, 4);
    List<Emp> emps = mapper.selectByExample(null);
    PageInfo<Emp> pageInfo=new PageInfo<>(emps,5);
    emps.forEach(System.out::println);
    System.out.println(pageInfo);
}
```

> PageInfo{pageNum=8, pageSize=4, size=2, 
>
> startRow=29, endRow=30, total=30, pages=8,
>
> list=Page{count=true, pageNum=8, pageSize=4,
>
> startRow=28, endRow=32, total=30 ,pages=8,
>
> reasonable=false, pageSizeZero=false},
>
> prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, 
>
> hasPreviousPage=true,hasNextPage=false, navigatePages=5, 
>
> navigateFirstPage4, navigateLastPage8, navigatepageNums=[4, 5, 6, 7, 8]}
>
> pageNum：当前页的页码
>
> pageSize：每页显示的条数
>
> size：当前页显示的真实条数
>
> total：总记录数
>
> pages：总页数
>
> prePage：上一页的页码
>
> nextPage：下一页的页码
>
> isFirstPage/isLastPage：是否为第一页/最后一页
>
> hasPreviousPage/hasNextPage：是否存在上一页/下一页
>
> navigatePages：导航分页的页码数
>
> navigatepageNums：导航分页的页码，[1,2,3,4,5]


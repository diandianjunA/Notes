#JDBC
##获取数据库连接
```java
public class ConnectionTest {
    //方式一
    @Test
    public void testConnection() throws SQLException {
        //获取Driver实现类的对象
        Driver driver=new com.mysql.cj.jdbc.Driver();
        String url = "jdbc:mysql://localhost:3306/test";
        Properties info = new Properties();
        info.setProperty("user","root");
        info.setProperty("password","Xjs02220012");
        Connection connect = driver.connect(url, info);
        System.out.println(connect);
    }
    //方式二
    @Test
    public void testConnection2() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, SQLException {
        //获取Driver实现类的对象，使用反射来实现
        Class<Driver> clazz= (Class<Driver>) Class.forName("com.mysql.cj.jdbc.Driver");
        Driver driver = clazz.getDeclaredConstructor().newInstance();
        //提供URL和Property
        String url = "jdbc:mysql://localhost:3306/test";
        Properties info = new Properties();
        info.setProperty("user","root");
        info.setProperty("password","Xjs02220012");
        Connection connect = driver.connect(url, info);
        System.out.println(connect);
    }
    //方式三
    @Test
    public void testConnection3() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, SQLException {
        //1.获取driver实现类的对象
        Class<Driver> clazz= (Class<Driver>) Class.forName("com.mysql.cj.jdbc.Driver");
        Driver driver = clazz.getDeclaredConstructor().newInstance();
        String url = "jdbc:mysql://localhost:3306/test";
        String user ="root";
        String password="Xjs02220012";
        //注册驱动
        DriverManager.registerDriver(driver);
        //获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);
    }
    //方式四
    @Test
    public void testConnection4() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, SQLException {
        //mysql的driver类的静态代码块中有自动将该driver对象加载到DriverManager中的操作
        Class.forName("com.mysql.cj.jdbc.Driver");
        String url = "jdbc:mysql://localhost:3306/test";
        String user ="root";
        String password="Xjs02220012";
        //获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);
    }
    //方式五:将数据库连接需要的4个基本信息声明在配置文件中，通过读取配置文件的方式获取连接
    @Test
    public void testConnection5() throws IOException, ClassNotFoundException, SQLException {
        //1.读取配置文件的信息
        InputStream resourceAsStream = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties properties=new Properties();
        properties.load(resourceAsStream);
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driverClass = properties.getProperty("driverClass");
        //2.加载驱动
        Class.forName(driverClass);
        //3.获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);
    }
}
```
## 增删改查

```java
@Test
public void PreparedStatementTest(){
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    try {
        //1.读取配置文件的信息
        InputStream resourceAsStream = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties properties=new Properties();
        properties.load(resourceAsStream);
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driverClass = properties.getProperty("driverClass");
        //2.加载驱动
        Class.forName(driverClass);
        //3.获取连接
        connection = DriverManager.getConnection(url, user, password);
        //4.预编译SQL语句，返回PreparedStatement的实例
        String sql="insert into customers(name,email,birth)value(?,?,?)";//?：占位符
        preparedStatement = connection.prepareStatement(sql);
        //5.填充占位符
        preparedStatement.setString(1,"美羊羊");
        preparedStatement.setString(2,"ganyu@gmail.com");
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        Date parse = simpleDateFormat.parse("3000-12-01");
        preparedStatement.setDate(3,new java.sql.Date(parse.getTime()));
        //6.执行sql
        preparedStatement.execute();
    } catch (IOException e) {
        throw new RuntimeException(e);
    } catch (ClassNotFoundException e) {
        throw new RuntimeException(e);
    } catch (SQLException e) {
        throw new RuntimeException(e);
    } catch (ParseException e) {
        throw new RuntimeException(e);
    } finally {
        //7.资源关闭
        try {
            preparedStatement.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        try {
            connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## 通用方法

```java
public class QueryTest {
    public List<Order> orderQuery(String sql,Object...args){
        List<Order> list= null;
        InputStream resourceAsStream = null;
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            list = new ArrayList<>();
            resourceAsStream = QueryTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
            Properties properties=new Properties();
            properties.load(resourceAsStream);
            String url=properties.getProperty("url");
            String user=properties.getProperty("user");
            String password=properties.getProperty("password");
            String driverClass=properties.getProperty("driverClass");
            Class.forName(driverClass);
            connection = DriverManager.getConnection(url, user, password);
            preparedStatement = connection.prepareStatement(sql);
            for(int i=0;i< args.length;i++){
                preparedStatement.setObject(i+1,args[i]);
            }
            resultSet = preparedStatement.executeQuery();
            ResultSetMetaData metaData = resultSet.getMetaData();
            int columnCount = metaData.getColumnCount();
            while(resultSet.next()){
                Order order = new Order();
                for(int i=0;i<columnCount;i++){
                    Object object = resultSet.getObject(i + 1);
                    Field field = Order.class.getDeclaredField(metaData.getColumnLabel(i + 1));
                    field.setAccessible(true);
                    field.set(order,object);
                }
                list.add(order);
            }
            return list;
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } catch (NoSuchFieldException e) {
            throw new RuntimeException(e);
        } catch (SecurityException e) {
            throw new RuntimeException(e);
        } catch (IllegalArgumentException e) {
            throw new RuntimeException(e);
        } catch (IllegalAccessException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                resourceAsStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
            try {
                resultSet.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    public void modifyOrder(String sql,Object...args){
        InputStream resourceAsStream = null;
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            resourceAsStream = QueryTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
            Properties properties=new Properties();
            properties.load(resourceAsStream);
            String url=properties.getProperty("url");
            String user=properties.getProperty("user");
            String password=properties.getProperty("password");
            String driverClass=properties.getProperty("driverClass");
            Class.forName(driverClass);
            connection = DriverManager.getConnection(url, user, password);
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                preparedStatement.setObject(i+1,args[i]);
            }
            preparedStatement.execute();
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                resourceAsStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }

    }
    @Test
    public void orderQueryTest(){
        String sql="select order_id orderId,order_name orderName,order_date orderDate from `order` where order_id=? or order_id=?;";
        List<Order> orders = orderQuery(sql, 1,2);
        Iterator<Order> iterator = orders.iterator();
        while (iterator.hasNext()){
            Order next = iterator.next();
            System.out.println(next);
        }
    }
    @Test
    public void orderInsertTest() throws ParseException {
        String sql="Insert into `order`(order_id,order_name,order_date)value(?,?,?)";
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        java.util.Date parse = simpleDateFormat.parse("2022-06-17");
        Order dd = new Order(5, "DD", new Date(parse.getTime()));
        modifyOrder(sql,dd.getOrderId(),dd.getOrderName(),dd.getOrderDate());
    }
    @Test
    public void orderUpdateTest(){
        String sql="update `order` set order_name=\"ee\" where order_id=?";
        modifyOrder(sql,5);
    }
    @Test
    public void orderDeleteTest(){
        String sql="delete from `order` where order_id=?";
        modifyOrder(sql,5);
    }
}

class Order{
    private int orderId;
    private String orderName;
    private Date orderDate;

    public Order() {
    }

    public Order(int orderId, String orderName, Date orderDate) {
        this.orderId = orderId;
        this.orderName = orderName;
        this.orderDate = orderDate;
    }

    public int getOrderId() {
        return orderId;
    }

    public void setOrderId(int orderId) {
        this.orderId = orderId;
    }

    public String getOrderName() {
        return orderName;
    }

    public void setOrderName(String orderName) {
        this.orderName = orderName;
    }

    public Date getOrderDate() {
        return orderDate;
    }

    public void setOrderDate(Date orderDate) {
        this.orderDate = orderDate;
    }

    @Override
    public String toString() {
        return "Order{" +
                "orderId=" + orderId +
                ", orderName='" + orderName + '\'' +
                ", orderDate=" + orderDate +
                '}';
    }
}
```

## 使用Druid和dbutils操作数据库

```java
public class DruidTest {
    public static Connection getConnection() throws Exception {
        InputStream systemResourceAsStream = ClassLoader.getSystemResourceAsStream("druid.properties");
        Properties properties = new Properties();
        properties.load(systemResourceAsStream);
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        return connection;
    }
}
public class QueryRunnerTest {
    @Test
    public void testInsert() throws Exception {
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql = "insert into customers(name,email,birth)value(?,?,?)";
            int insertCount = queryRunner.update(connection, sql, "蔡徐坤", "caixukun@126com", "1997-09-08");
            System.out.println(insertCount);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            connection.close();
        }
    }
    @Test
    public void testQuery1(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select id,name,email,birth from customers where id=?";
            BeanHandler<Customer> customerBeanHandler = new BeanHandler<Customer>(Customer.class);
            Customer query = queryRunner.query(connection, sql, customerBeanHandler,19);
            System.out.println(query);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if(connection!=null){
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    @Test
    public void testQuery2(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select id,name,email,birth from customers where id<=?";
            BeanListHandler<Customer> beanListHandler = new BeanListHandler(Customer.class);
            for (Customer customer : queryRunner.query(connection, sql, beanListHandler, 19)) {
                System.out.println(customer);
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (connection!=null) {
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
  @Test
    public void testQuery3(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select id,name,email,birth from customers where id=?";
            MapHandler mapHandler = new MapHandler();
            Map<String, Object> query = queryRunner.query(connection, sql, mapHandler, 19);
            System.out.println(query);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if(connection!=null){
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    @Test
    public void testQuery4(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select id,name,email,birth from customers where id<=?";
            MapListHandler mapListHandler = new MapListHandler();
            for (Map<String, Object> stringObjectMap : queryRunner.query(connection, sql, mapListHandler, 19)) {
                System.out.println(stringObjectMap);
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (connection!=null) {
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
  @Test
    public void testQuery5(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select count(*) from customers";
            ScalarHandler scalarHandler = new ScalarHandler();
            Object query = queryRunner.query(connection, sql, scalarHandler);
            System.out.println(query);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (connection!=null) {
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    @Test
    public void testQuery6(){
        Connection connection = null;
        try {
            QueryRunner queryRunner = new QueryRunner();
            connection = DruidTest.getConnection();
            String sql="select min(birth) from customers";
            ScalarHandler scalarHandler = new ScalarHandler();
            Object query = queryRunner.query(connection, sql, scalarHandler);
            System.out.println(query);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (connection!=null) {
                    connection.close();
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```


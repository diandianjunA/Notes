# JavaWeb

![](http://www.diandianjun.com.cn:8080/resource/blog/text/Java/javaWeb.jpg)

##filter层

CharacterEncodingFilter

用于修改web端的字符集

```java
@WebFilter(value = "*.do")
public class CharacterEncodingFilter implements Filter {
    private String characterEncoding="UTF-8";
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        String encodingStr=filterConfig.getInitParameter("encoding");
        if(!StringUtils.isEmpty(encodingStr)){
            characterEncoding=encodingStr;
        }
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        ((HttpServletRequest)servletRequest).setCharacterEncoding(characterEncoding);
        filterChain.doFilter(servletRequest,servletResponse);

    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }
}
```

OpenSessionViewFilter

用于开启，提交，回滚事务

```java
@WebFilter("*.do")
public class OpenSessionViewFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        try{
            TransactionManager.beginTrans();
            System.out.println("开启事务");
            filterChain.doFilter(servletRequest,servletResponse);
            TransactionManager.submit();
            System.out.println("提交事务");
        }catch (Exception e){
            e.printStackTrace();
            try {
                System.out.println("回滚事务");
                TransactionManager.rollback();
            } catch (SQLException ex) {
                throw new RuntimeException(ex);
            } catch (ClassNotFoundException ex) {
                throw new RuntimeException(ex);
            }
        }
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }
}
```

TransactionManager

开启提交回滚事务的具体执行

```java
public class TransactionManager {

    //开启事务
    public static void beginTrans() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnectionFromThreadLocal();
        connection.setAutoCommit(false);
    }
    //提交事务
    public static void submit() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnectionFromThreadLocal();
        connection.commit();
        JDBCUtils.deleteConnectionFromThreadLocal();
    }
    //回滚事务
    public static void rollback() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnectionFromThreadLocal();
        connection.rollback();
        JDBCUtils.deleteConnectionFromThreadLocal();
    }
}
```
## servlet层

DispatcherServlet

中央调度器，用于接收servlet请求并调用正确的controller

```java
@WebServlet("*.do")
public class DispatcherServlet extends ViewBaseServlet {
    private BeanFactory beanFactory;

    @Override
    public void init() throws ServletException {
        super.init();
        ServletContext servletContext = getServletContext();
        Object beanFactoryObj = servletContext.getAttribute("beanFactory");
        if(beanFactoryObj!=null){
            beanFactory=(BeanFactory) beanFactoryObj;
        }else{
            throw new RuntimeException("IOC容器获取失败");
        }
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) {
        String servletPath = req.getServletPath();
        servletPath = servletPath.substring(1);
        int i = servletPath.lastIndexOf(".do");
        servletPath=servletPath.substring(0,i);
        Object o = beanFactory.getBean(servletPath);
        String operator = req.getParameter("operator");
        if(StringUtils.isEmpty(operator)){
            operator="index";
        }
        try {
            Method[] declaredMethods = o.getClass().getDeclaredMethods();
            for (int j = 0; j < declaredMethods.length; j++) {
                if(operator.equals(declaredMethods[j].getName())){
                    //获取当前方法参数列表
                    Parameter[] parameters = declaredMethods[j].getParameters();
                    Object[] parameterValues = new Object[parameters.length];
                    for (int k = 0; k < parameterValues.length; k++) {
                        String parameterName = parameters[k].getName();
                        if("req".equals(parameterName)){
                            parameterValues[k]=req;
                        }else if("resp".equals(parameterName)){
                            parameterValues[k]=resp;
                        }else if("session".equals(parameterName)){
                            parameterValues[k]=req.getSession();
                        }else {
                            String Value = req.getParameter(parameterName);
                            if(Value !=null){
                                String typeName = parameters[k].getType().getName();
                                Class typeClass = Class.forName(typeName);
                                Object parameterValue = typeClass.getDeclaredConstructor(String.class).newInstance(Value);
                                parameterValues[k] = parameterValue;
                            }else {
                                parameterValues[k]=null;
                            }
                        }
                    }
                    declaredMethods[j].setAccessible(true);
                    Object returnObj = declaredMethods[j].invoke(o, parameterValues);
                    String returnStr=(String) returnObj;
                    if(returnStr.startsWith("redirect:")){
                        String redirectStr = returnStr.substring("redirect:".length());
                        resp.sendRedirect(redirectStr);
                    }else {
                        super.processTemplate(returnStr,req,resp);
                    }
                }
            }
        } catch (ClassNotFoundException | InstantiationException | IllegalAccessException | InvocationTargetException |
                 NoSuchMethodException | IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## controller层

接收来自中央调度器的调用以及参数，调用业务层的执行函数，并返回对应的跳转指令

```java
public class FruitController{
//    private ServletContext servletContext;
//
//    public void setServletContext(ServletContext servletContext) throws ServletException {
//        this.servletContext = servletContext;
//        super.init(servletContext);
//    }

    private FruitService fruitService=null;

    protected String index(HttpServletRequest req,String keyWord,Integer pageNo) throws ServletException, IOException, SQLException, ClassNotFoundException, NoSuchFieldException, InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException {
        HttpSession session = req.getSession();
//        String pageNoStr = req.getParameter("pageNo");
//        int pageNo = 1;
//        if(pageNoStr!=null){
//            pageNo=Integer.parseInt(pageNoStr);
//        }66
        if(pageNo==null){
            pageNo=1;
        }
        session.setAttribute("pageNo",pageNo);
        List<Fruit> fruitList = fruitService.getFruitList(keyWord,pageNo);
        session.setAttribute("FruitList",fruitList);
        int pageCount=fruitService.getPageCount(keyWord);
        session.setAttribute("pageCount",pageCount);
//      super.processTemplate("index",req,resp);
        return "index";
    }
    protected String add(String fname,BigDecimal price,BigDecimal fcount,String remark) throws IOException, SQLException, ClassNotFoundException {
        Fruit fruit = new Fruit(fname, price, fcount, remark);
        fruitService.addFruit(fruit);
//      resp.sendRedirect("fruit.do");
        return "redirect:fruit.do";
    }
    protected String delete(String fname) throws IOException, SQLException, ClassNotFoundException {
        fruitService.deleteFruitByName(fname);
//      resp.sendRedirect("fruit.do");
        return "redirect:fruit.do";
    }
    protected String edit(String fname,HttpServletRequest req) throws IOException, SQLException, ClassNotFoundException, NoSuchFieldException, InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException {
        if(fname!=null&&!"".equals(fname)){
            Fruit fruit = fruitService.getFruitByName(fname);
            req.setAttribute("fruit",fruit);
            //super.processTemplate("edit",req,resp);
            return "edit";
        }
        return "error";
    }
    protected String update(String fnamePre,String fname,BigDecimal price,BigDecimal fcount,String remark) throws SQLException, IOException, ClassNotFoundException {
        Fruit fruit = new Fruit(fname,price,fcount,remark);
        fruitService.updateFruit(fruit,fnamePre);
//      resp.sendRedirect("fruit.do");
        return "redirect:fruit.do";
    }
}
```

## service层

通过调用DAO层完成对应的业务功能的具体实现

```java
public class FruitServiceImplement implements FruitService {
    private FruitDAO fruitDAO=null;

    @Override
    public List<Fruit> getFruitList(String keyWord, Integer pageNo) throws SQLException, NoSuchFieldException, InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        System.out.println("getFruitList->"+connection);
        return fruitDAO.getFruitList(connection, pageNo, keyWord);
    }

    @Override
    public void addFruit(Fruit fruit) throws SQLException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        fruitDAO.addFruit(connection,fruit);
    }

    @Override
    public Fruit getFruitByName(String fname) throws SQLException, NoSuchFieldException, InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        return fruitDAO.getFruitByName(connection,fname);
    }

    @Override
    public void deleteFruitByName(String fname) throws SQLException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        fruitDAO.deleteFruitByFruitName(connection,fname);
    }

    @Override
    public Integer getPageCount(String keyWord) throws SQLException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        System.out.println("getFruitList->"+connection);
        int fruitCount = fruitDAO.getFruitCount(connection,keyWord);
        int pageCount=(fruitCount+5-1)/5;
        return pageCount;
    }

    @Override
    public void updateFruit(Fruit fruit,String fruitName) throws SQLException, IOException, ClassNotFoundException {
        Connection connection=JDBCUtils.getConnectionFromThreadLocal();
        fruitDAO.updateFruitByName(connection,fruit,fruitName);
    }
}
```

## DAO层

BaseDAO

封装的具体的JDBC的增删改查操作

```java
public class BaseDAO {
    public void update(Connection connection,String sql,Object...args) throws SQLException {
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for(int i=0;i< args.length;i++){
            preparedStatement.setObject(i+1,args[i]);
        }
        preparedStatement.execute();
    }
    public void add(Connection connection,String sql,Object object) throws SQLException, IllegalAccessException {
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        Field[] fields = object.getClass().getDeclaredFields();
        for (int i = 0; i < fields.length; i++) {
            fields[i].setAccessible(true);
            Object o = fields[i].get(object);
            preparedStatement.setObject(i+1,o);
        }
        preparedStatement.execute();
    }
    public int countQuery(Connection connection,String sql,Object...args) throws SQLException {
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        int count = 0;
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i+1,args[i]);
        }
        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        if (resultSet.next()){
            count=resultSet.getInt(1);
        }
        JDBCUtils.closeResource(null,preparedStatement,resultSet);
        return count;
    }
    public <T> List<T> Query(Connection connection,Class<T> clazz,String sql,Object...args) throws SQLException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, NoSuchFieldException {
        PreparedStatement preparedStatement=connection.prepareStatement(sql);
        List<T> list=new ArrayList<>();
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i+1,args[i]);
        }
        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        while(resultSet.next()) {
            T t = clazz.getDeclaredConstructor().newInstance();
            for (int i = 0; i < columnCount; i++) {
                Object object = resultSet.getObject(i + 1);
                Field declaredField = t.getClass().getDeclaredField(metaData.getColumnLabel(i + 1));
                declaredField.setAccessible(true);
                declaredField.set(t, object);
            }
            list.add(t);
        }
        JDBCUtils.closeResource(null,preparedStatement,resultSet);
        return list;
    }

    @Test
    public void insertTest() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnection();
        String sql="insert into fruit(FruitName,Price,Repertory,Remark)value(?,?,?,?)";
//        update(connection,sql,"apple",5,20,"nothing");
        update(connection,sql,"watermelon",10,200,"sweet");
        update(connection,sql,"orange",6,21,"sour");
        update(connection,sql,"strawberry",15,200,"best");
        JDBCUtils.closeResource(connection,null,null);
    }
    @Test
    public void updateTest() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnection();
        String sql="update fruit set Repertory=? where FruitName=?";
        update(connection,sql,25,"watermelon");
        JDBCUtils.closeResource(connection,null,null);
    }
    @Test
    public void deleteTest() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnection();
        String sql1="insert into fruit(FruitName,Price,Repertory,Remark)value(?,?,?,?)";
        String sql2="delete from fruit where FruitName=?";
//        update(connection,sql1,"testFruit",10,10,"none");
        update(connection,sql2,"testFruit");
        JDBCUtils.closeResource(connection,null,null);
    }
    @Test
    public void queryTest() throws SQLException, IOException, ClassNotFoundException, NoSuchFieldException, InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException {
        Connection connection = JDBCUtils.getConnection();
        String sql="select FruitName fruitName,Price price,Repertory repertory,Remark remark from fruit where Price<?";
        for (Fruit fruit : Query(connection, Fruit.class, sql, 15)) {
            System.out.println(fruit);
        }
    }
    @Test
    public void addTest() throws SQLException, IOException, ClassNotFoundException, IllegalAccessException {
        Connection connection = JDBCUtils.getConnection();
        String sql="insert into fruit(FruitName,Price,Repertory,Remark)value(?,?,?,?)";
        Fruit fruit = new Fruit("banana", new BigDecimal(12), new BigDecimal(24), "I love it");
        add(connection,sql,fruit);
        JDBCUtils.closeResource(connection,null,null);
    }
    @Test
    public void complexQueryTest() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = JDBCUtils.getConnection("jdbc.properties");
        String sql="select count(*) from fruit";
        int i = countQuery(connection, sql);
        System.out.println(i);
        JDBCUtils.closeResource(connection,null,null);
    }
}
class Fruit{
    private String fruitName;
    private BigDecimal price;
    private BigDecimal repertory;
    private String remark;

    public Fruit() {
    }

    public Fruit(String fruitName, BigDecimal price, BigDecimal repertory, String remark) {
        this.fruitName = fruitName;
        this.price = price;
        this.repertory = repertory;
        this.remark = remark;
    }

    public String getFruitName() {
        return fruitName;
    }

    public void setFruitName(String fruitName) {
        this.fruitName = fruitName;
    }

    public BigDecimal getPrice() {
        return price;
    }

    public void setPrice(BigDecimal price) {
        this.price = price;
    }

    public BigDecimal getRepertory() {
        return repertory;
    }

    public void setRepertory(BigDecimal repertory) {
        this.repertory = repertory;
    }

    public String getRemark() {
        return remark;
    }

    public void setRemark(String remark) {
        this.remark = remark;
    }

    @Override
    public String toString() {
        return "Fruit{" +
                "fruitName='" + fruitName + '\'' +
                ", price=" + price +
                ", repertory=" + repertory +
                ", remark='" + remark + '\'' +
                '}';
    }
}
```

JDBCutils

管理有关connection的创建与关闭，同时创造ThreadLocal来节约资源

```java
public class JDBCUtils {
    private static ThreadLocal<Connection> threadLocal=new ThreadLocal<>();
    public static Connection getConnection() throws IOException, ClassNotFoundException, SQLException {
        String user = "root";
        String url = "jdbc:mysql://localhost:3306/fruit";
        String password = "Xjs02220012";
        String driverClass = "com.mysql.cj.jdbc.Driver";
        Class.forName(driverClass);
        return DriverManager.getConnection(url, user, password);
    }
    public static Connection getConnection(String propertiesUrl) throws IOException, ClassNotFoundException, SQLException {
        InputStream resourceAsStream = JDBCUtils.class.getClassLoader().getResourceAsStream(propertiesUrl);
        Properties properties = new Properties();
        properties.load(resourceAsStream);
        String user = properties.getProperty("user");
        String url = properties.getProperty("url");
        String password = properties.getProperty("password");
        String driverClass = properties.getProperty("DriverClass");
        Class.forName(driverClass);
        return DriverManager.getConnection(url, user, password);
    }
    public static void closeResource(Connection connection, PreparedStatement preparedStatement, ResultSet resultSet){
        if(connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
        if(preparedStatement!=null){
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
        if(resultSet!=null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    public static Connection getConnectionFromThreadLocal() throws SQLException, IOException, ClassNotFoundException {
        Connection connection = threadLocal.get();
        if(connection==null){
            connection = getConnection("jdbc.properties");
            threadLocal.set(connection);
        }
        return connection;
    }
    public static void deleteConnectionFromThreadLocal() throws SQLException {
        Connection connection = threadLocal.get();
        if(connection==null){
            return;
        }
        if(!connection.isClosed()){
            connection.close();
            threadLocal.set(null);
        }
    }
    @Test
    public void testUtils() throws SQLException, IOException, ClassNotFoundException {
        Connection connection=getConnection();
        System.out.println(connection);
        closeResource(connection,null,null);
    }
}
```

## listener层

初始化ioc容器，完成资源的链接

```java
@WebListener
public class ContextLoaderListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        String path = servletContext.getInitParameter("contextConfigLocation");
        BeanFactory beanFactory=new ClassPathXmlApplicationContext(path);
        servletContext.setAttribute("beanFactory",beanFactory);
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        ServletContextListener.super.contextDestroyed(sce);
    }
}
```

## ioc容器

完成资源链接

```java
public class ClassPathXmlApplicationContext implements BeanFactory{
    private Map<String,Object> beanMap=new HashMap<>();
    private String path="applicationContext.xml";
    public ClassPathXmlApplicationContext(){
        this("applicationContext.xml");
    }
    public ClassPathXmlApplicationContext(String path){
        if(StringUtils.isEmpty(path)){
            throw new RuntimeException("ioc路径错误");
        }
        try {
            InputStream resourceAsStream = getClass().getClassLoader().getResourceAsStream(path);
            DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
            DocumentBuilder documentBuilder=documentBuilderFactory.newDocumentBuilder();
            Document document = documentBuilder.parse(resourceAsStream);
            //获取所有bean节点
            NodeList beans = document.getElementsByTagName("bean");
            //获取所有的bean节点
            for (int i = 0; i < beans.getLength(); i++) {
                Node item = beans.item(i);
                if(item.getNodeType()==Node.ELEMENT_NODE){
                    Element beanElement=(Element) item;
                    String id = beanElement.getAttribute("id");
                    String className = beanElement.getAttribute("class");
                    Class BeanClass = Class.forName(className);
                    Object o = BeanClass.getDeclaredConstructor().newInstance();
//                    Method setServletContext = controllerBeanClass.getDeclaredMethod("setServletContext", ServletContext.class);
//                    setServletContext.invoke(o,this.getServletContext());
                    beanMap.put(id,o);
                }
            }
            //组装bean之间的依赖关系
            for (int i = 0; i < beans.getLength(); i++) {
                Node item = beans.item(i);
                if (item.getNodeType() == Node.ELEMENT_NODE) {
                    Element beanElement = (Element) item;
                    String id = beanElement.getAttribute("id");
                    NodeList childNodes = beanElement.getChildNodes();
                    for (int j = 0; j < childNodes.getLength(); j++) {
                        Node item1 = childNodes.item(j);
                        if(item1.getNodeType()==Node.ELEMENT_NODE&&"property".equals(item1.getNodeName())){
                            Element property=(Element) item1;
                            String propertyName = property.getAttribute("name");
                            String ref = property.getAttribute("ref");
                            //找到ref对应的实例
                            Object refObj = beanMap.get(ref);
                            //将refObj设置到当前bean对应的实例的property属性上
                            Object beanObj = beanMap.get(id);
                            Field propertyField = beanObj.getClass().getDeclaredField(propertyName);
                            propertyField.setAccessible(true);
                            propertyField.set(beanObj,refObj);
                        }
                    }
                }
            }
        } catch (ParserConfigurationException | IOException | SAXException | ClassNotFoundException |
                 InvocationTargetException | InstantiationException | IllegalAccessException | NoSuchMethodException |
                 NoSuchFieldException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public Object getBean(String id) {
        return beanMap.get(id);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<beans>
<!--    标签的作用是 servletPath中涉及的名字对应的是fruit，那么就要FruitController处理-->
    <bean id="fruit" class="mvcServlet.FruitController">
        <property name="fruitService" ref="fruitService"/>
    </bean>
    <bean id="fruitDAO" class="Demo1Servlet.FruitDAO"></bean>
    <bean id="fruitService" class="biz.implement.FruitServiceImplement">
        <!-- property标签用来表示属性：name表示属性名；ref表示引用其他bean的id -->
        <property name="fruitDAO" ref="fruitDAO"/>
    </bean>
</beans>
```


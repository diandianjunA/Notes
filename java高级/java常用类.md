# java常用类

## String类

### String类的特点以及不可变性

String是一个final类，代表不可变的字符序列，且不可被继承

字符串是常量，用双引号引起来表示

String对象的字符串内容是存储在一个字符数组value[]中的

String实现了Serializable接口：表示字符串是支持序列化的

String实现了Comparable接口：表示字符串是可以比较大小

### String对象的创建与赋值

- String str = "hello";

  字面值定义法：String用字面量定义方式时，对象处于方法区的字符串常量池，当两个字符串内容相同时，它们的地址值是相等的，因为常量池中不会存放两个相同内容的字符串，修改字符串内容时，并不是把原地址存放的字符串内容进行修改，而是在常量池中新创建一个新内容的字符串，并修改原变量的地址，进行字符串拼接时，也是重新指定了一块内存区域设定新内容，而不是在原地址后面加或修改原地址内的字符串的值，字符串调用replace方法时，同上。即，原地址的字符串是不可变的。无论怎么修改，都是重新造了一个字符串，并制定了一块新的内存区域

- String s1=new String();

- String s2=new String(String orginal);

- String s3=new String(char[] a);

- String s4=new String(char[] a,int startIndex,int count);

  new创建对象+构造器的定义方式，此时是在堆空间内创造了一个对象，并将地址值给那个对象，此时对象内是一个地址值，指向常量区内的字符串

### 字符串的拼接

- 如果是常量与常量之间拼接，结果在常量池中
- 如果拼接过程中有变量参与，则结果在堆空间中
- 如果处在堆空间中，调用intern()方法，就会转移到常量池中

==说明：虽然String类型的变量本质是地址值，但把它传入函数中的形参时，通过地址修改字符串内容，原字符串内容并不会修改，原因是由于字符串的不可变性，该变量不会通过地址值去修改内存区域的内容，而是会重新开辟一块内存区域，再将该区域的地址赋给该变量，因此无法通过形参方式修改原字符串的内容==

==注：如果字符串声明时加了final，则视作常量，按照第一种情况处理==

### String类中的常用方法

- int length():返回字符串长度
- char charAt(int index):返回某索引处的字符
- boolean isEmpty():判断是否为空字符串
- String toLowerCase():将字符串内的字符转换为小写
- String toUpperCase():将字符串内的字符转换为大写
- String concat(String str):将指定字符串连接到此字符串结尾，等价于用"+"
- String trim():返回字符串的副本，忽略前导空白和尾部空白，==注：这四个都是返回一个转换后的字符串，而不是在原有的字符串基础上修改==
- boolean equals(Object obj):比较字符串内容是否相同
- boolean equalsIgnoreCase(String anotherString):忽略大小写，比较字符串内容是否相同
- int compareTo(String anotherString):比较两个字符串的大小
- String substring(int beginIndex):返回一个新字符串，它是此字符串从beginIndex开始截取到结尾的字符串
- String substring(int beginIndex,int endIndex):返回一个新字符串，它是此字符串从beginIndex到endIndex截取的字符串，==不包含最后一个字符==
- boolean endWith(String suffix):测试此字符串是否以指定后缀结束
- boolean startsWith(String prefix):测试此字符串是否以指定前缀开始
- boolean startsWith(String prefix,int toffset):测试此字符串从指定索引开始的子字符串是否以指定前缀开始
- boolean contains(CharSequence s):当且仅当此字符串包含指定的char值序列时返回true
- int indexOf(String str):返回指定子字符串在此字符串中第一次出现的索引
- int indexOf(String str,int fromIndex):返回指定子字符串在此字符串中从指定位置起第一次出现的索引
- int lastIndexOf(String str):返回指定子字符串在此字符串中最后一次出现的索引
- int lastIndexOf(String str,int fromIndex):返回指定子字符串在此字符串中从指定位置起反向搜索第一次出现的索引
- String replace(char oldChar,char newChar):返回一个新字符串，用newChar替换所有的oldChar
- String replace(CharSequence target,CharSequence replacement):返回一个新字符串，用新的字符序列替换所有的原有的字符序列

### String与其他类型转换

- String转换为基本数据类型或者包装类：调用包装类的parseXxx(String str)方法，如int num=Integer.parseInt(str)
- 基本数据类型、包装类转换为String类型：调用String类型的valueOf()方法，如String s=String.valueOf(Integer)
- String转换为char[]数组：调用String类型的toCharArray()方法，如char[] charArray=str1.toCharArray
- char[]数组转换为String：调用String的构造器
- String转换为byte[]数组：调用String的getBytes()(默认字符集)或getBytes("gbk")
- byte[]数组转换为String：调用String的构造器String(byte)或String(byte,"gbk")

## StringBuffer与StringBuilder类

### String，StringBuffer，StringBuilder的异同

String:不可变的字符序列，底层使用char[]数组存储

StringBuffer:可变的字符序列；线程安全的，效率较低，底层使用char[]数组存储

StringBuilder:可变的字符序列；jdk5.0新增，线程不安全，效率高，底层使用char[]数组存储

源码分析：

- String str=new String();//char[] value=new char[0];
- String str1=new String("abc");//char[] value=new char[]{'a','b','c'};
- StringBuffer sb1=new StringBuffer()//char[] value=new char[16],底层创建了一个长度是16的char[]数组
  - sb1.append('a');//value[0]='a';
  - sb1.append('b');//value[1]='b';
- StringBuffer sb2=new StringBuffer("abc")//char[] value=new char["abc".length()+16];

说明：

- 输出sb1.length()时，不会包含多出来的那16个（StringBuffer重写了length方法）
- 如果添加的数据底层数组装不下了，就需要扩容，默认情况下，扩容为原来的2倍+2，同时将原有数组中的元素复制到新数组中
- 指导意义：建议使用StringBuffer(int capacity)或StringBuilder(int capacity)

### StringBuffer类的常用方法

- StringBuffer append(xxx):进行字符串拼接
- StringBuffer delete(int start,int end):删除指定位置的内容(不包含最后一个字符)
- StringBuffer replace(int start,int end,String str):把[start,end]位置替换为str(不包含最后一个字符)
- StringBuffer insert(int offset,xxx):在指定位置插入xxx(插在前面)
- StringBuffer reverse():把当前字符序列逆转
- public int indexOf(String str)
- public String substring(int start,int end)
- public int length()
- public char charAt(int n)
- public void setCharAt(int n,char ch)

效率对比：StringBuilder>StringBuffer>String

## 日期时间API

### System类

```java
public class DateTimeTest {
    @Test
    public void test1(){
        long time=System.currentTimeMillis();
        //返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差
        //称为时间戳
        System.out.println(time);
    }
}
```

### Date类

java.util.Date类

1. 两个构造器的使用：
   - Date():创建一个对应当前时间的Date对象
   - Date(long time):创建指定毫秒数的Date对象
2. 两个方法的使用：
   - toString():显示当前年月日时分秒
   - getTime():获取当前Date对象的时间戳

```java
@Test
public void test2(){
    Date date1 = new Date();
    System.out.println(date1.toString());
    System.out.println(date1.getTime());
    Date date2=new Date(1651888615260L);
    System.out.println(date2);
}
```

java.sql.Date类：对应这数据库中的日期类型的变量

```java
@Test
public void sqlDateTest(){
    //创建java.sql.Date对象
    java.sql.Date date = new java.sql.Date(5151516555L);
    System.out.println(date);
    //java.util.Date与java.sql.Date转换
    //情况一：通过多态与强制类型转换
    Date date1=date;
    java.sql.Date date2=(java.sql.Date)date1;
    //情况二：通过构造器进行转换
    Date date3=new Date();
    java.sql.Date date4=new java.sql.Date(date3.getTime());
}
```

### java.text.SimpleDateFormat类

- Date类的API不易于国际化，大部分被废弃了，java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类
- 它允许进行格式化：日期->文本、解析：文本->日期

```java
@Test
public void test3() throws ParseException {
    //实例化SimpleDateFormat，使用默认构造器
    SimpleDateFormat simpleDateFormat=new SimpleDateFormat();
    //格式化：日期->字符串
    Date date = new Date();
    System.out.println(date);
    String format = simpleDateFormat.format(date);
    System.out.println(format);
    //解析：格式化的逆过程：字符串->日期
    String str="2022/5/7 下午1:34";
    Date parse = simpleDateFormat.parse(str);
    System.out.println(parse);
    //按照指定方式进行格式化
    SimpleDateFormat simpleDateFormat1 = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
    String format1 = simpleDateFormat1.format(date);
    System.out.println(format1);
    //解析：要求字符串必须是符合SimpleDateFormat识别的格式(通过构造器参数体现)，否则会抛异常
    Date parse1 = simpleDateFormat1.parse("2022.05.07 14:18:27");
    System.out.println(parse1);
    System.out.println(parse1.getTime());
}
```

### Calender日历类的使用

Calender类是一个抽象类

```java
@Test
    public void testCalender(){
        //1.实例化
        //方式一：创建其子类（GregorianCalender)的对象
        //方式二：调用其静态方法getInstance()
        Calendar calendar=Calendar.getInstance();
//        System.out.println(calendar.getClass());
        //2.常用方法
        //get()，获取一些常用信息
        int i = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(i);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));
        //set(),修改Calender内某些属性
        calendar.set(Calendar.DAY_OF_MONTH,8);
        int i1 = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(i1);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));
        //add()，某些属性加上多少
        calendar.add(Calendar.DAY_OF_MONTH,3);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
        //getTime():日历类转换为Date类
        Date time = calendar.getTime();
        System.out.println(time);
        //setTime():Date类转换为日历类
        Date date = new Date();
        calendar.setTime(date);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
    }
```

注意：获取月份时：一月是0，二月是1......获取星期时：周日是1，周一是2,....周六是7

### JDK8中新日期时间API

Date类和Calender类面临的问题

- 可变性：日期和时间应该是不可变的
- 偏移性：Date中的年份从1900年开始，月份都从0开始
- 格式化：格式化只对Date有用，Calender则不行
- 此外，它们都不是线程安全的，不能处理闰秒

#### LocalDate,LocalTime,LocalDateTime的使用(类似于Calender类)

```java
@Test
public void test4(){
    //now():获取当前的日期、时间、日期+时间
    LocalDate localDate=LocalDate.now();
    LocalTime localTime = LocalTime.now();
    LocalDateTime localDateTime = LocalDateTime.now();
    System.out.println(localDate);
    System.out.println(localTime);
    System.out.println(localDateTime);
    //of():设置指定的年月日时分秒，没有偏移量
    LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 13, 23, 43);
    System.out.println(localDateTime1);
    //getXxx()
    System.out.println(localDateTime.getDayOfMonth());
    System.out.println(localDateTime.getDayOfWeek());
    System.out.println(localDateTime.getDayOfYear());
    System.out.println(localDateTime.getMonth());
    System.out.println(localDateTime.getMonthValue());
    System.out.println(localDateTime.getMinute());
    //体现不可变性
    //withXxx():设置相关属性
    LocalDate localDate1 = localDate.withDayOfMonth(22);
    System.out.println(localDate);
    System.out.println(localDate1);
    LocalDateTime localDateTime2 = localDateTime.withHour(4);
    System.out.println(localDateTime);
    System.out.println(localDateTime2);
    //plusXxx(),minusXxx(),加减相关属性，同样有不可变性
    LocalDateTime localDateTime3 = localDateTime.plusMonths(3);
    System.out.println(localDateTime);
    System.out.println(localDateTime3);
    LocalDateTime localDateTime4 = localDateTime.minusDays(6);
    System.out.println(localDateTime);
    System.out.println(localDateTime4);
}
```

#### Instant(类似于Date类)

```java
@Test
public void test5(){
    Instant instant = Instant.now();
    System.out.println(instant);//本初子午线的时间
    //添加时间偏移量
    OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
    System.out.println(offsetDateTime);
    //获取时间戳->Date类的getTime()
    long milli = instant.toEpochMilli();
    System.out.println(milli);
    //ofEpochMilli():通过给定的时间戳，获取Instant实例->Date(long millis)
    Instant instant1 = Instant.ofEpochMilli(1651910763597L);
    System.out.println(instant1);
}
```

#### DateTimeFormatter(类似于SimpleDateFormat类)

用于格式化或解析日期、时间

```java
@Test
public void test6(){
    //实例化方式一：预定义的标准格式。如：ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME
    DateTimeFormatter isoLocalDateTime = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
    //格式化:日期->字符串
    LocalDateTime localDateTime = LocalDateTime.now();
    String str1 = isoLocalDateTime.format(localDateTime);
    System.out.println(localDateTime);
    System.out.println(str1);
    //解析：字符串->日期
    TemporalAccessor temporalAccessor = isoLocalDateTime.parse("2022-05-07T16:19:41.404584800");
    System.out.println(temporalAccessor);

    //实例化方式二：本地化相关的格式。如：ofLocalizedDateTime(FormatStyle.LONG)
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
    //格式化
    String format = dateTimeFormatter.format(localDateTime.atZone(ZoneId.systemDefault()));
    System.out.println(format);
    //实例化方式三：自定义的格式。如：ofPattern("yyyy-MM-dd hh:mm:ss E")
    DateTimeFormatter dateTimeFormatter1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss E");
    //格式化
    String format1 = dateTimeFormatter1.format(LocalDateTime.now());
    System.out.println(format1);
    //解析
    TemporalAccessor temporalAccessor1 = dateTimeFormatter1.parse("2022-05-07 16:36:20 周六");
    System.out.println(temporalAccessor1);
}
```

## java比较器

### Comparable接口的使用

1. 像String、包装类等实现了Comparable接口，重写了compareTo()的方法，给出了比较两个对象大小的方式
2. 重写compareTo(obj)的规则：如果当前对象this大于形参对象obj，则返回正整数，小于返回负整数，相等返回0（这时是从小到大排列）

```java
@Test
public void test1(){
    String[] arr=new String[]{"AA","CC","MM","KK","GG","JJ","DD"};
    Arrays.sort(arr);
    System.out.println(Arrays.toString(arr));
}
```

如果自定义类需要排序，可以让自定义类实现Comparable接口，重写compareTo(obj)方法，在compareTo(obj)指明排序方法。先用instance of判断是否为相同类型的实例，再用强制类型转换转为同一类型，再根据属性进行比较

### Comparator定制排序

当元素的类型没有实现Comparable接口，但又不方便修改代码，或者实现了Comparable接口的排序规则不适合当前的操作，那么可以考虑用Comparator对象来排序

```java
@Test
public void test2(){
    String[] arr=new String[]{"AA","CC","MM","KK","GG","JJ","DD"};
    Arrays.sort(arr, new Comparator() {
        //按照字符串从大到小的顺序排列
        @Override
        public int compare(Object o1, Object o2) {
            if(o1 instanceof String&&o2 instanceof String){
                String s1=(String) o1;
                String s2=(String) o2;
                return -s1.compareTo(s2);
            }
            throw new RuntimeException("输入的数据类型不一致");
        }
    });
    System.out.println(Arrays.toString(arr));
}
```

## 其他类

System类，Math类，BigInteger类，BigDecimal类
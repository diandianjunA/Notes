#Internet

```java
/**
 * 一、网络编程中有两个主要的问题：
 * 1.如何准确地定位网络上一台或多台主机；定位主机上的特定的应用
 * 2.找到主机后如何可靠高效地进行数据传输
 *
 * 二、网络编程中的两个要素：
 * 1.对应问题一：IP和端口号
 * 2.对应问题二：提供网络通信协议：TCP/IP参考模型（应用层、传输层、网络层、物理+数据链路层）
 *
 *
 * 三、通信要素一：IP和端口号
 *
 * 1. IP:唯一的标识 Internet 上的计算机（通信实体）
 * 2. 在Java中使用InetAddress类代表IP
 * 3. IP分类：IPv4 和 IPv6 ; 万维网 和 局域网
 * 4. 域名:   www.baidu.com   www.mi.com  www.sina.com  www.jd.com
 *            www.vip.com
 * 5. 本地回路地址：127.0.0.1 对应着：localhost
 *
 * 6. 如何实例化InetAddress:两个方法：getByName(String host) 、 getLocalHost()
 *        两个常用方法：getHostName() / getHostAddress()
 *
 * 7. 端口号：正在计算机上运行的进程。
 * 要求：不同的进程有不同的端口号
 * 范围：被规定为一个 16 位的整数 0~65535。
 *
 * 8. 端口号与IP地址的组合得出一个网络套接字：Socket
 */
public class InetAddressTest {
    public static void main(String[] args) {
        try {
            InetAddress byName = InetAddress.getByName("192.168.10.14");
            System.out.println(byName);
            InetAddress byName1 = InetAddress.getByName("www.baidu.com");
            System.out.println(byName1);
            InetAddress byName2 = InetAddress.getByName("127.0.0.1");
            System.out.println(byName2);
            //获取本机地址
            InetAddress localHost = InetAddress.getLocalHost();
            System.out.println(localHost);
            //getHostName():域名
            System.out.println(byName1.getHostName());
            //getHostAddress():IP地址
            System.out.println(byName1.getHostAddress());
        } catch (UnknownHostException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## TCP编程

1.发送信息并接收

```java
public class TCPTest {
    //客户端
    @Test
    public void client() {

        Socket socket= null;
        OutputStream outputStream = null;
        try {
            InetAddress inetAddress=InetAddress.getByName(InetAddress.getLocalHost().getHostName());
            //1.创建Socket对象，指明服务器的IP及端口号
            socket = new Socket(inetAddress,8899);
            //2.获取一个输出流，用于输出数据
            outputStream = socket.getOutputStream();
            //3.写出数据操作
            outputStream.write("远上寒山石径斜".getBytes(StandardCharsets.UTF_8));
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //4.资源的关闭
            if(outputStream!=null){
                try {
                    outputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(socket!=null){
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
    //服务端
    @Test
    public void server() {
        ServerSocket serverSocket = null;
        Socket socket= null;
        InputStream inputStream = null;
        ByteArrayOutputStream byteArrayOutputStream= null;
        try {
            //1.创建服务器端的ServerSocket，指明自己的端口号
            serverSocket = new ServerSocket(8899);
            //2.调用accept方法，接收来自客户端的socket
            socket = serverSocket.accept();
            //3.获取输入流
            inputStream = socket.getInputStream();
            //转换流方法
//        char[] buffer=new char[20];
//        int len;
//        InputStreamReader inputStreamReader=new InputStreamReader(inputStream);
//        while((len=inputStreamReader.read(buffer))!=-1){
//            String str=new String(buffer,0,len);
//            System.out.println(str);
//        }
            //byteArrayOutputStream方法
            //4.读取输入流中的数据
            byteArrayOutputStream = new ByteArrayOutputStream();
            byte[] buffer=new byte[5];
            int len;
            while((len=inputStream.read(buffer))!=-1){
                byteArrayOutputStream.write(buffer,0,len);
            }
            System.out.println(byteArrayOutputStream.toString());
            System.out.println(socket.getInetAddress().getHostAddress());
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //关闭资源
            if(byteArrayOutputStream!=null){
                try {
                    byteArrayOutputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(inputStream!=null){
                try {
                    inputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(socket!=null){
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(serverSocket!=null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

2.发送文件接收并保存
```java
public class TCPTest2 {
    @Test
    public void client() {
        Socket socket= null;
        OutputStream outputStream = null;
        FileInputStream fileInputStream= null;
        try {
            //1.造一个socket
            socket = new Socket(InetAddress.getLocalHost(),9090);
            //2.获取一个输出流
            outputStream = socket.getOutputStream();
            //3.获取一个文件输入流
            fileInputStream = new FileInputStream(new File("8539517.jpg"));
            //4.读入文件，再写出
            byte[] buffer=new byte[1024];
            int len;
            while((len=fileInputStream.read(buffer))!=-1){
                outputStream.write(buffer,0,len);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //5.资源关闭
            if(fileInputStream!=null){
                try {
                    fileInputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(outputStream!=null) {
                try {
                    outputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(socket!=null){
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
    @Test
    public void server() {
        ServerSocket serverSocket = null;
        Socket socket = null;
        InputStream inputStream = null;
        FileOutputStream fileOutputStream= null;
        try {
            //1.造一个ServerSocket
            serverSocket = new ServerSocket(9090);
            //2.获取一个客户端的Socket
            socket = serverSocket.accept();
            //3.获取一个客户端的输入流
            inputStream = socket.getInputStream();
            //4.保存文件到本地，于是建一个FileOutputStream将文件保存
            fileOutputStream = new FileOutputStream("copy1.jpg");
            //5.读写过程
            byte[] buffer=new byte[1024];
            int len;
            while((len=inputStream.read(buffer))!=-1){
                fileOutputStream.write(buffer,0,len);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //6.资源关闭
            if(fileOutputStream!=null) {
                try {
                    fileOutputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(fileOutputStream!=null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(fileOutputStream!=null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(fileOutputStream!=null) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```
```java
public class TCPTest3 {
    @Test
    public void client() {
        Socket socket = null;
        OutputStream outputStream = null;
        FileInputStream fileInputStream = null;
        ByteArrayOutputStream byteArrayOutputStream = null;
        InputStream inputStream = null;
        try {
            //1.造一个socket
            socket = new Socket(InetAddress.getLocalHost(), 9090);
            //2.获取一个输出流
            outputStream = socket.getOutputStream();
            //3.获取一个文件输入流
            fileInputStream = new FileInputStream(new File("8539517.jpg"));
            //4.读入文件，再写出
            byte[] buffer = new byte[1024];
            int len;
            while ((len = fileInputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, len);
            }
            //由于服务器接收端的read方法是阻塞型方法，需要客户端给出传输完成的信号才会终止，否则会一直等待客户端继续传输，上一个测试中客户端发送完毕后socket被关闭了
            //因此在关闭socket时向服务器的read返回了-1，使得服务器可以正常关闭，而该测试中客户端发送完后并没有发送结束信号,而是等待服务端发送数据，服务端又在等待客户端发送数据，就
            //造成了死锁
            socket.shutdownOutput();
            //5.接收来自服务器端的数据，并显示在控制台上
            inputStream = socket.getInputStream();
//            InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
//            char[] buffer1=new char[5];
//            while((len=inputStreamReader.read(buffer1))!=-1){
//                String str=new String(buffer1,0,len);
//                System.out.print(str);
//            }
            byteArrayOutputStream = new ByteArrayOutputStream();
            byte[] buffer1 = new byte[5];
            System.out.println("hello");
            while ((len = inputStream.read(buffer1)) != -1) {
                byteArrayOutputStream.write(buffer1, 0, len);
            }
            System.out.println(byteArrayOutputStream.toString());
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //6.资源关闭
            if (byteArrayOutputStream != null) {
                try {
                    byteArrayOutputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (inputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (fileInputStream != null) {
                try {
                    fileInputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (outputStream != null) {
                try {
                    outputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
    @Test
    public void server() {
        ServerSocket serverSocket = null;
        Socket socket = null;
        InputStream inputStream = null;
        FileOutputStream fileOutputStream = null;
        OutputStream outputStream = null;
        try {
            //1.造一个ServerSocket
            serverSocket = new ServerSocket(9090);
            //2.获取一个客户端的Socket
            socket = serverSocket.accept();
            //3.获取一个客户端的输入流
            inputStream = socket.getInputStream();
            //4.保存文件到本地，于是建一个FileOutputStream将文件保存
            fileOutputStream = new FileOutputStream("copy2.jpg");
            //5.读写过程
            byte[] buffer = new byte[1024];
            int len;
            while ((len = inputStream.read(buffer)) != -1) {
                fileOutputStream.write(buffer, 0, len);
                System.out.println("hello");
            }
            //6.服务端给予客户端反馈

            outputStream = socket.getOutputStream();
            outputStream.write("你好，照片已收到,非常好".getBytes(StandardCharsets.UTF_8));
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //7.资源关闭
            if (outputStream != null) {
                try {
                    outputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (fileOutputStream != null) {
                try {
                    fileOutputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (fileOutputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (fileOutputStream != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if (fileOutputStream != null) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

## UDP编程

###一、UDP协议简介

UDP（UserDatagramProtocol）是一个简单的面向消息的传输层协议，尽管UDP提供标头和有效负载的完整性验证（通过校验和），但它不保证向上层协议提供消息传递，并且UDP层在发送后不会保留UDP 消息的状态。因此，UDP有时被称为不可靠的数据报协议。如果需要传输可靠性，则必须在用户应用程序中实现。

UDP是基于IP的简单协议，不可靠的协议。
UDP的优点：简单，轻量化。
UDP的缺点：没有流控制，没有应答确认机制，不能解决丢包、重发、错序问题。
综上所述，UDP使用场景为流媒体应用、语音交流、视频会议所使用的传输层协议，还有许多基于互联网的电话服务使用的VOIP（基于IP的语音）也是基于UDP运行的，实时视频和音频流协议旨在处理偶尔丢失的数据包，因此，如果重新传输丢失的数据包，则只会发生质量略有下降，而不是出现较大的延迟。

###二、DatagramSocket 类

DatagramSocket 类用于表示发送和接收数据报包的套接字。数据报包套接字是包投递服务的发送或接收点。每个在数据报包套接字上发送或接收的包都是单独编址和路由的。从一台机器发送到另一台机器的多个包可能选择不同的路由，也可能按不同的顺序到达。

####一 、构造方法

（1）DatagramSocket()
绑定到本地地址和一个随机的端口号

（2）DatagramSocket(int port)
绑定本地地址和一个特定的端口号

（3）DatagramSocket(int port, InetAddress iad)
绑定到特定的端口号及指定地址

（4）DatagramSocket(SocketAddress sad)
绑定指定地址和随机端口号

####二、基本方法

（1）close()
关闭套接字

（2）recevie(DatagramPacket dp)
接受数据报

（3）send(DatagramPacket dp)
发送数据报

###三、DatagramPacket 类

java.net 包中的 DatagramPacket 类用来表示数据报包，数据报包用来实现无连接包投递服务。每条报文仅根据该包中包含的信息从一台机器路由到另一台机器。从一台机器发送到另一台机器的多个包可能选择不同的路由，也可能按不同的顺序到达。

####一、接受类型

（1）DatagramPacket(byte[] buf, int length)
用来接受长度为length的buf数据(即数据存于字节数组buf中)

####二、发送类型

（1）DatagramPacket(byte[] buf, int length, InetAddress address, int port)
将length长的buf数据发送到指定的地址的端口号处

（2）DatagramPacket(byte[] buf, int length, SocketAddress address)
将length长的buf数据发送到指定的套接字地址处

###四、代码示例

1、一个发送方，一个接收方
2、先运行接收方，再运行发送方

UdpReceive

```java
public static void main(String[] args) throws IOException {
	DatagramSocket datagramSocket = new DatagramSocket(1111); 
	DatagramPacket datagramPacket = new DatagramPacket(new byte[1024], 1024);
	datagramSocket.receive(datagramPacket); // 接收数据

	byte[] arr = datagramPacket.getData(); // 获取数据
	int len = datagramPacket.getLength(); // 获取有效长度
	System.out.println(new String(arr, 0, len));
	datagramSocket.close(); // 关闭
}
```
UdpSend

```java
public static void main(String[] args) throws IOException { // 异常处理
	String str = "我是UDP发送的文件";
	DatagramSocket datagramSocket = new DatagramSocket(); // 创建DatagramSocket
	DatagramPacket datagramPacket = new DatagramPacket(str.getBytes(),
			str.getBytes().length, InetAddress.getByName("127.0.0.1"), 1111); // 创建DatagramPacket（要发送的数据，数据的长度，Ip地址，端口）
	datagramSocket.send(datagramPacket); // 发送
	datagramSocket.close(); // 关闭
}
```

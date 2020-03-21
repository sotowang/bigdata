# [socket协议位于哪一层](https://bbs.csdn.net/topics/340056488)

* SOCKET 应该算不上是个协议，应该是应用层与传输层间的一个抽象层，是个编程接口。



# Socket相关面试题

编写一个网络应用程序,有客户端与服务器端,客户端向服务器发唐太宗一个字符串,服务器收到该字符串后将其打印到命令行上,然后向客户端返回该字符串的长度,最后,客户端输出服务器端返回的该字符串的长度,分别用TCP和UDP两种方式去实现.

* TCP

  * TCPServer.java

  ```java
  public class TCPServer{
      ServerSocket ss = new ServerSocket(65000);
      while(true){
          Socket socket = ss.accept();
          //业务逻辑类
          new LengthCalculator(socket).start();
      }
  }
  ```

  * TCPClient.java

  ```java
  public class TCPClient{
      public static void main(String[] args) throws Exception{
          Socket socket = new Socket("127.0.0.1",65000);
          OutputStream os = socket.getOutputStrean();
          InputStream is = socket.getInputStream();
          os.write(new String("hello world").getBytes());
          int ch = 0;
          byte[] buff = new byte[1024];
          ch = is.read(buff);
          String content = new String(buff,0,ch);
          System.out.println(content);
          
          is.close();
          os.close();
          socket.close();
      }
  }
  ```

  
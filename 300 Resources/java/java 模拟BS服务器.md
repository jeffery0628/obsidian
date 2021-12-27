---
Create: 2021年 十月 30日, 星期六 15:03
tags: 
  - Engineering/java
  - 大数据
---
## 模拟B\S服务器

模拟网站服务器，使用浏览器访问自己编写的服务端程序，查看网页效果。

### 案例分析

1. 准备页面数据，web文件夹，复制到我们Module中。

	![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-internet/%E5%A4%8D%E5%88%B6.png)

2. 模拟服务器端，ServerSocket类监听端口，使用浏览器访问

	~~~java
	public static void main(String[] args) throws IOException {
	    	ServerSocket server = new ServerSocket(8000);
	    	Socket socket = server.accept();
	    	InputStream in = socket.getInputStream();
	   	    byte[] bytes = new byte[1024];
	    	int len = in.read(bytes);
	    	System.out.println(new String(bytes,0,len));
	    	socket.close();
	    	server.close();
	}
	~~~

3. 通过访问localhost:8000，服务器程序中字节输入流可以读取到浏览器发来的请求信息

	![](../../../../../../../Documents/blog/source/_posts/engineering/java/java-internet/读取访问信息.jpg)


GET/web/index.html HTTP/1.1是浏览器的请求消息。/web/index.html为浏览器想要请求的服务器端的资源,使用字符串切割方式获取到请求的资源。

~~~java
//转换流,读取浏览器请求第一行
BufferedReader readWb = new BufferedReader(new InputStreamReader(socket.getInputStream()));
String requst = readWb.readLine();
//取出请求资源的路径
String[] strArr = requst.split(" ");
//去掉web前面的/
String path = strArr[1].substring(1);
System.out.println(path);
~~~

### 实现

服务端实现：

```java
public class SerDemo {
    public static void main(String[] args) throws IOException {
        System.out.println("服务端  启动 , 等待连接 .... ");
        // 创建ServerSocket 对象
        ServerSocket server = new ServerSocket(8888);
        Socket socket = server.accept();
        // 转换流读取浏览器的请求消息
        BufferedReader readWb = new
        BufferedReader(new InputStreamReader(socket.getInputStream()));
        String requst = readWb.readLine();
        // 取出请求资源的路径
        String[] strArr = requst.split(" ");
        // 去掉web前面的/
        String path = strArr[1].substring(1);
        // 读取客户端请求的资源文件
        FileInputStream fis = new FileInputStream(path);
        byte[] bytes= new byte[1024];
        int len = 0 ;
        // 字节输出流,将文件写会客户端
        OutputStream out = socket.getOutputStream();
        // 写入HTTP协议响应头,固定写法
        out.write("HTTP/1.1 200 OK\r\n".getBytes());
        out.write("Content-Type:text/html\r\n".getBytes());
        // 必须要写入空行,否则浏览器不解析
        out.write("\r\n".getBytes());
        while((len = fis.read(bytes))!=-1){
            out.write(bytes,0,len);
        }
        fis.close();
        out.close();
        readWb.close();	
        socket.close();
        server.close();
    }
}

```

### 访问效果

> 小贴士：不同的浏览器，内核不一样，解析效果有可能不一样。

发现浏览器中出现很多的叉子,说明浏览器没有读取到图片信息导致。浏览器工作原理是遇到图片会开启一个线程进行单独的访问,因此在服务器端加入线程技术。

~~~java
public class ServerDemo {
    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(8888);
        while(true){
            Socket socket = server.accept();
            new Thread(new Web(socket)).start();
        }
    }
    static class Web implements Runnable{
        private Socket socket;

        public Web(Socket socket){
            this.socket=socket;
        }

        public void run() {
            try{
                //转换流,读取浏览器请求第一行
                BufferedReader readWb = new
                        BufferedReader(new InputStreamReader(socket.getInputStream()));
                String requst = readWb.readLine();
                //取出请求资源的路径
                String[] strArr = requst.split(" ");
                System.out.println(Arrays.toString(strArr));
                String path = strArr[1].substring(1);
                System.out.println(path);

                FileInputStream fis = new FileInputStream(path);
                System.out.println(fis);
                byte[] bytes= new byte[1024];
                int len = 0 ;
                //向浏览器 回写数据
                OutputStream out = socket.getOutputStream();
                out.write("HTTP/1.1 200 OK\r\n".getBytes());
                out.write("Content-Type:text/html\r\n".getBytes());
                out.write("\r\n".getBytes());
                while((len = fis.read(bytes))!=-1){
                    out.write(bytes,0,len);
                }
                fis.close();
                out.close();
                readWb.close();
                socket.close();
            }catch(Exception ex){

            }
        }
    }

}
~~~



[[200 Areas/230 Engineering/232 大数据/java|java 目录]]


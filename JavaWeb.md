# JavaEE
## 软件架构的分类
- C/S架构：client/server(qq，网游)，也叫胖客户端
  - 优点：能充分利用客户端的资源，服务器的压力相对较小
  - 缺点：维护成本高
- B/S架构：browser/server，也叫瘦客户端
  - 优点：易于维护，不需要在客户端安装应用程序
  - 缺点：服务器压力大
## HTTP协议(必须深入了解)
- Hypertext Transfer Protocol，超文本传输协议
- 基于TCP/IP之上的应用层的协议
- HTTP协议是基于请求/响应(request/response)的处理方式的协议
- HTTP协议是一个无状态，无连接的协议
- 工作原理：
  - 建立TCP连接
  - 客户端发出HTTP请求
  - 服务端响应
  - 服务端关闭连接
- HTTP1.1的特点
  - 在一个TCP连接上可以传送多个HTTP请求和响应、
  - 多个请求和响应过程可以重叠进行
  - 增加了更多的请求头和响应头
## 并发和并行
- 并发：一个网站在一段时间的访问量
- 并行：1000w个用户一起访问一个网站，这个网站的服务器同时处理了1000w个请求，这叫并行
## HTTP的结构
### 请求(request)的规范
- 一个请求行，若干个标题头，[一个请求体]
- 请求行：
  - GET url 协议版本，例如：GET index.jsp HTTP/1.1(GET方式传递的内容在请求行上) POST(POST方式传递的内容在请求体中，更安全，且请求行有长度限制，当内容很长时，也建议使用POST方式)
- 消息头：客户端给服务端提供的一些额外信息
- 请求体：客户端给服务端发送的数据内容
### 响应(response)的规范
- 状态行：HTTP/1.1 200 OK
- 多个消息头(服务端给客户端的)
- 一个空行
- 实体内容
```Java
Content-Type:text/plain;charset=utf-8 // 这是纯文本
Content-Type:text/html;charset=utf-8 //这是html
```
## 环境搭建
- 开发工具(IntelliJ IDEA)
- Tomcat服务器
## Servlet
- applet：运行在网页里的小程序
- servlet：运行在服务器端(Tomcat等等)的小程序，作用是用来接收客户的请求，并对客户端进行响应
- MIDlet
## Tomcat的目录结构
### bin：Tomcat的各个命令
### lib：Tomcat所依赖的jar包
### config：Tomcat的配置文件
### logs：工作日志
### temp：临时文件存储目录
### webapps：web应用
### work：jsp文件的工作目录
## 什么是Servlet
- 广义上来说，Servlet是一组规范，包含了与服务端小程序相关的很多API
- 狭义上来说，Servlet是运行在服务器端(Tomcat)的小程序；它的作用是用来接收客户端的请求，并对客户端做出响应；通常是使用HTTP协议来进行通信
### 如何开发Servlet
- 定义一个类，来继承GenericServlet或HttpServlet；重写doGet或者doPost方法
- 配置Servlet的映射url(可以使用web.xml(Servlet不能重名)，也可以使用@WebServlet注解)
- **注意，在使用IDEA自动创建@WebServlet时候，里面的value需要配置好**
```Java
@WebServlet(name = "BookServlet",value="/book.do")
public class BookServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request,response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.getWriter().append("Served at : ").append(request.getContextPath());
    }
}
```
### ServletRequest
- 此接口封装了所有客户端请求数据(请求行、消息头、内容体)；这个对象由容器(Tomcat)创建，并将此对象以参数的形式传递给Servlet的service、doGet、doPost方法；如果我们使用的是HTTP协议，可以使用HttpServletRequest接口
### HttpServletRequest的常用方法
- getParameter():根据请求参数名称来获得请求值
- getParameterNames():获得所有的请求名
- getParameterMap():获得请求数据的映射
- getParameterValues():获得多个请求值
- **request中文乱码原理及解决方法[Google](https://blog.csdn.net/lxf512666/article/details/52939573)**，简单来说，客户端先使用UTF-8编码，再使用ISO-8859-1编码，要想中文不乱码，就要先使用ISO-8859-1解码，再使用UTF-8解码，代码demo如下：
```Java
String bookName = request.getParameter("bookname");
System.out.println("name:" + new String(bookName.getBytes("ISO-8859-1"),"UTF-8"));
```
### ServletResponse
- response是由容器创建，并以参数的形式返回传递给Servlet的service方法，response封装了很多向Client返回响应的方法
- 主要的方法有：
  - setContentType():设置向Client返回的数据的类型，setContentType("text/html";charset="UTF-8");
  - getWriter():获得字符流
  - getOutputStream();
  - sendRedirect();重定向
### Servlet的生命周期
- 实例化(创建)
- 初始化(init()方法调用) **在第一次请求之前执行，一个Servlet对象只调用一次init()方法**
- 提供服务(service() doGet() doPost())
- 销毁(destroy()) **在服务器移除Servlet时调用，也只调用一次**
- 垃圾回收
- 注：init()方法默认是在第一次接受请求时调用，也可以通过设置loadOnStartUp()=正数，来配置是否在服务器启动时调用，1的优先级最高
### Servlet是由容器创建，每个Servlet只创建一个唯一的实例；而service()是多线程的方法，即多个客户端同时发送请求时，会自动启动多个线程来处理
### Tomcat来调用哪个Servlet的service()方法，service的结果返回给Tomcat，由Tomcat返回给客户端
## 会话(Session)跟踪
### 什么是会话
- 有始有终的一系列动作
- 一个会话包含了多次请求
### Servlet中实现Session跟踪机制的方式
- HttpSession接口
### 如何获得一个Session
- Session是由服务器创建的
- HttpSession session = request.getSession();
### HttpSession中常用的方法
- getId():获得这个Session的唯一标识，此id是由Servlet创建的
- getCreationTime():获得这个Session的创建时间
- getLastAccessedTime():获得这个最后一次访问的时间
- getMaxInactiveInterval():获得最大时间间隔
- setMaxInactiveInterval():获得最大时间间隔
- isNew():是否是新创建的
- invalidate():让Session失效
- setAttribute(String name,Object value):向Session中存值
- getAttribute(String name):获得Session中的数据
- removeAttribute(String name):移除Session中的数据
- getAttributeNames():获得Session中所有数据的绑定名称
### 设置Session过期时间的方法
- getMaxInactiveInterval()
- web.xml文件：可以设置这个工程下所有的Session的过期时间都是xxx分钟
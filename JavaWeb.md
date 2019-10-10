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
- 配置Servlet的映射url(可以使用web.xml，也可以使用@WebServlet注解)
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
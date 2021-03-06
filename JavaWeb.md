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
### 最佳实践
- 用户登录：将用户的信息存到Session中；如果想解决不同用户的Session过期时间不同，可以在数据库那张表中添加session字段，设置不同的值，在登陆时设置即可。
- 购物车
## Cookie
- Cookie中可以存储少量的信息；Cookie是由服务端向客户端(Browser)发送的信息，浏览器获得这个信息后，再自动传回给服务器
- Cookie可以做会话跟踪；还可以做购物车，自动登录等等
### 如何操作Cookie
- 创建Cookie；名；值；maxAge=-1(同浏览器存活时间相同)
- 向客户端(Browser)发送Cookie；response.addCookie();
- 从客户端接收Cookie，request.getCookies();
### Cookie和Session的关系
#### Cookie是实现Session跟踪的一种方式
## ServletContext
- 是Servlet上下文对象
- 通过它，我们可以访问web容器；每个应用只有一个ServletContext对象，这个对象可以被所有的Servlet共享
### ServletContext的方法也分为两类
#### 1. 存储数据
- setAttribute()
- getAttribute()
- removeAttribute()
- getAttributeNames()
#### 2. 与容器通信的
- getRealPath():将虚拟路径转换成一个真实路径
- getContextPath():当前应用的路径
### 如何获得ServletContext
```Java
// 获得上下文的两种方式
request.getServletContext();
request.getSession().getServletContext();
```
## 作用域对象有三个
- request：请求结束就失效
- session：取决于Session的存活时间
- servletContext：只有一份，与服务器(Tomcat)同生共死，且所有的Servlet共享
## ServletConfig
### 就是Servlet的一些配置，在web.xml中配置，或者在Annotation中配置，然后再init(ServletConfig config)中得到
---
## JSP(Java Server Page)
### 本质上来说，JSP就是一个Servlet，但看上去更像html，JSP更适合编写服务端运行的页面
**我的jsp对应的.java文件存放在C:\Users\ClarinetZuo\.IntelliJIdea2019.1\system\tomcat\Unnamed_JavaWebLearn_2\work\Catalina\localhost\JavaWebLearn_war_exploded\org\apache\jsp**
### JSP注释
- html注释 <!-- html注释 -- >
- 隐藏注释(JSP->Servlet时，消失) <%-- 隐藏注释 --%>
### 脚本元素
#### 就是如何在JSP中插入Java代码
- 声明
```Java
<%! 声明属性或方法； %> 声明属性和方法
```
- 代码段
```Java
<% Java 代码 %> 这里的Java代码是放在生成的Servlet的service方法中
```
- 表达式
```Java
<%= 表达式 %> 在页面中输出一个表达式的结果
```
### JSP指令
#### JSP指令的作用是告诉JSP容器(Tomcat)如何处理整个JSP文档
#### JSP指令有三种
- page指令
```
<%@ page 属性名="值" %>
info:描述信息
language:语言(默认java)
contentType:"text/html;charset=UTF-8"
extends:父类
import:引入非lang包的类
session:此页面是否支持Session，默认为true
buffer:缓冲区的大小
autoFlush:是否自动刷新
isThreadSafe:是否线程安全
errorPage:
isErrorPage:
```
- include指令
```
<%@include%>
```
- taglib指令
```
<%@ taglib%> 可以使用第三方的标签
```
### JSP的内置对象
#### 内置对象也被称为隐式对象，内置对象就是不用定义，可以直接使用的对象
#### JSP的9个内置对象
- out,response
- page,request,session,application
- config,exception,pageContext
#### JSP的作用域对象
- page:当前页面
- request:当次请求
- session:当次会话
- application:应用
#### pageContext对象
- 页面的上下文对象，通过此对象可以对四个作用域对象进行操作
- 还可以实现页面的转发
- forward和sendRedirect的区别：前者是一次请求，sendRedirect是两次请求；forward只能在服务器内部转发，sendRedirect可以重定向到任何资源；forward时地址栏不变，sendRedirect地址栏发生变化
- include指令和include方法的区别：指令最终生成一个Servlet文件，include方法生成两个Servlet文件，外部Servlet调用内部的Servlet
- 重定向的状态码是302
#### 在Servlet中转发请求
```Java
RequestDispatcher requestDispatcher = req.getRequestDispatcher("bookList.jsp");
requestDispatcher.forward(req,resp);
```
### EL表达式
#### EL：Expression Language(表达式语言)
#### 作用：在JSP中通过一个简单的表达式来操作各作用域对象的数据
#### 语法
- ${el表达式}：从page作用域开始查找对象，page -> request -> session -> application，如果都没有，返回null；如果只想在某个作用域查找，可以使用pageScope.xxx
- .：访问属性
- []：可以获得数据的元素或者属性
- 如果使用EL表达式来获取对象的属性值时，实际调用的是getXxx()方法，而不是属性本身
```Java
${sessionScope.book.bookName} -> book.getBookName()
```
- ${empty book.bookName} 返回bookName这个属性是否为null或者空
#### JSTL(JSP Standard Tag Library)
- c:核心标记
- fmt:格式化
- sql
- xml
- fn
##### 使用方法
- 使用<%@ taglib %> 来导入标记
```Java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```
- 使用标记
```Java
<!-- out -->
<c:out value="${book.isbn}" default="没有编号"></c:out>
<!-- 多分支 -->
<c:choose>
    <c:when test="${book.price > 50}">
        这本书的价格大于50元
    </c:when>
    <c:otherwise>
        两个分支
    </c:otherwise>

</c:choose>
```
- forEach标记
	- 作用：从集合(数组，迭代器，枚举器，Collection，Map，String)中遍历数据
	- 属性有：
    	- items:需要遍历的集合，使用el表达式
    	- var:每次遍历到的元素的变量名
    	- begin:起始位置
    	- end:结束位置
    	- step:每次遍历的步长
    	- varStatus:用来存储遍历到的那个元素的相关信息(下标、计数、是否是最后一个、是否是第一个等信息)
    	- index:索引，从0开始
    	- count:第几个，从1开始
```Java
<c:forEach items="${set}" var="setEle" varStatus="state">
    <tr>
        <td>${state.index}</td>
        <td>${setEle.isbn}</td>
        <td>${setEle.bookName}</td>
        <td>${setEle.price}</td>
        <td>操作</td>
    </tr>
</c:forEach>
```
- 日期格式化
```Java
<fmt:formatDate value="${date}" pattern="yyyy-MM-dd HH:mm:ss"/>
```
---
## MVC设计模式
### M
#### Model
- 处理业务逻辑；Dao，Service...
### V
#### View
- 展示数据(与用户交互)；JSP
### C
#### Controller
- 控制调试(获得用户数据，调用Model，请求转发或者重定向给View)；Servlet
### MVC的优点
- 维护性好(修改任何一部分不会影响到其他部分)
- 更利于分工合作
- 更利于代码复用
---
## Filter
### Filter是一个在请求之前和响应之后执行过滤任务的对象
### Filter的生命周期
- 实例化
- init()，只执行一次
- doFilter()，被调用多次
- destroy()，只执行一次
### 如何开发一个Filter
- 定义一个Filter类；实现doFilter()方法
- 配置映射：/admin/ *.do
---
## Listener(监听器)
### 监听各作用域对象(request,session,application)的变化，可以监听每个对象的两个行为
- 生命周期(create,destroy)
- 属性的改变(add,remove,replace)
### Listener常用的接口
#### Session
- HttpSessionListener
  - sessionCreated(HttpSessionEvent se)
  - sessionDestroyed(HttpSessionEvent se)
- HttpSessionAttributeListener
  - attributeAdded(HttpSessionBindingEvent sbe)
  - attributeRemoved(HttpSessionBindingEvent sbe)
  - attributeReplaced(HttpSessionBindingEvent sbe)
- ServletContext
  - ServletContextListener
  - ServletContextAttributeListener
#### case
- 统计在线人数
- 统计登录人数及人名;
---
## 分页
### 查询结果只包含当页的数据
```sql
select * from tb_book limit 10,10;
```
### 查询出记录总数
```sql
select count(*) from tb_book;
```
### 开发分页功能
#### 定义一个分页信息类(PageInfo)，此类封装了所有的分页数据，包括List，当前页，总页数，总记录数，URL
```Java
request.setAttribute("page",pageInfo)
```
---
## xml(Extensible Markup Language)
### xml的主要用途
- 存储数据
- 配置文件(properties,annotation)
- 数据交换(WebService底层就用了xml，json前后端交互)
### xml语法
#### 结构
- 声明
```xml
<?xml version="1.0" encoding="UTF-8">
```
- 定义
- 注释
```xml
<!-- -->
```
- 内容
```xml
<根标记>
	<各种标记>
		...
	</各种标记>
</根标记>
```
### xml的有效性
- 结构良好：符合xml语法规范要求的
- 有效的：符合验证文件要求的(待验证的)
### 实体(以&开始,;结尾)
- lt <
- gt >
- amp &
- apos '
- quot "
### CDATA节
#### CDATA节中的内容会被xml所忽略，不被当做xml内容来进行解析
```xml
<![CDATA[
	我是一个java代码
	int a = b<<c;
]]>
```
### DTD(Document Type Defination)文件
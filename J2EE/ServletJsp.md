# servlet

## 代码错误解决方案

```
  	<!-- 
  		web工程错误代码解决方案
  			注意：html不允许有存在划黄线的标签
  			html修改不需要关闭Tomcat
  			Java代码和web.xml修改必须关闭Tomcat 修改好之后重新开启Tomcat
  			
  			404：
  				404：后面没有跟随路径
  					表示Tomcat开启失败
  						失败原因
  						a) web.xml配置错误
  						b）Tomcat已经开启了一个
  						c）Tomcat文件缺失
  				后面跟着路径
  					我发找到资源 action提交路径与URL-pattern 路径不匹配
  					标准路径出现错误 http：//localhost:8080/工程名/页面地址
  			
  			500：Java代码编译出错
  				或者在web.html文件中的<servrlt-class>节点下没有找到
  				servlet的是实例地址
  				can not found ** Class Exception
  			
  			405：get --doPost()
  				post --doPost()
  				method属性书写错误
  	 -->
  </body>
</html>

```

## web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>ServletDay1_base</display-name>
  
  
  <servlet>
  	<!-- 
  		3）对应 servlet-padding节点下的servlet-name的值，由此找到servlet具体的路径开始执行
  	 -->
  	<servlet-name>suibianxie</servlet-name>
  	<!-- 
  		4) 这个节点写的是具体的路径 可以按住ctrl键跳转到servlet服务中 跳转不过去 路径错误
  	 -->
  	<servlet-class>com.etoak.servel.Login</servlet-class>
  </servlet>
  
  <servlet-mapping>
  	<!-- 
  		2）此节点对应servlet下的servlet-name的值，可以随便写，但必须一样
  	 -->
  	<servlet-name>suibianxie</servlet-name>
  	<!-- 
  		1) 这块节点对应表单或者链接提交的URL地址
  			当Tomcat开启之后，开始监听这个节点配置的URL路径，
  			如果表单提交的路径与之匹配 则开始拦截 此处多了一个/ 反斜杠的意思是代表根目录
  			拦截成功之后找到servlet-name下的值 
  	 -->
  	<url-pattern>/servlet/Login</url-pattern>
  </servlet-mapping>
  
  
  <!-- 第二对  -->
  <servlet>
  	<servlet-name>Reg</servlet-name>
  	<servlet-class>com.etoak.servel.Reg</servlet-class>
  </servlet>
  <servlet-mapping>
  	<servlet-name>Reg</servlet-name>
  	<url-pattern>/servlet/Reg</url-pattern>
  </servlet-mapping>
  
  <!-- 第三对  -->
  <servlet>
  	<servlet-name>ShowAll</servlet-name>
  	<servlet-class>com.etoak.servel.ShowAll</servlet-class>
  </servlet>
  <servlet-mapping>
  	<servlet-name>ShowAll</servlet-name>
  	<url-pattern>/servlet/ShowAll</url-pattern>
  </servlet-mapping>
  
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

## servlet的跳转

### [1]  请求转发

```
servlet执行请求转发之后，跳转到另一个servlet或者页面，由于是servlet转发到目的地，但是浏览器不知道，所以浏览器的地址栏依然显示的是跳转之前的路径
servlet里面的方法跳转之前是do*** 跳转之后还是***
因为是同一次请求，所以Request范围依旧有效
请求转发没有固定的路径书写格式
	如果经历过请求转发，则之后的跳转路径全部书写为绝对路径
```

### [2] 重定向

```
servlet返回响应，浏览器重新地位到新的目的地 浏览器发送了两次请求 不再是同一种请求，Request的范围失效
跳转之前的方法是do***跳转之后的是doget
浏览器的地址栏显示的最终的路径地址
重定向的跳转路径一般是绝对路径 ---/工程名/servlet/类名 || /工程名/***.html ||/工程名/***.jsp
```

![两种跳转方式](D:\yitupeixun\java课程\前端\ServletDaysOfficial\ServletDay3\attachment\两种跳转方式.jpg)

## servlet的四种范围及其局限性

### [1] PageContext

​	跳转之后立刻失效

### [2] HttpServletRequest

​	请求转发依然有效，因为是同一请求

​	重定向之后无效，因为不是同一请求

### [3] HttpSession

​	只要不超过最大的活动周期，依然有效

### [4] ServletContext

​	只要Tomcat没有关闭，依然有效

![四种范围](D:\yitupeixun\java课程\前端\ServletDaysOfficial\ServletDay3\attachment\四种范围.jpg)

## servlet的两种跟踪机制

### [1] cookie

```
是由客户端浏览器提供的提供的一种回话跟踪机制，用来保存数据，这些数据可以用来设置用户的权限。并且，cookie存在实体，最终保存在用户的本地，我们可以找到cookie的文件，默认不支持中文
Cookie cookie  = new Cookie(String,String);
创建Cookie之后，设置生命周期，根据生命周期开始倒计时，到期cookie销毁，内部数据无法保存。在此期间用户关闭计算机，浏览器的都不会影响cookie的生命周期
```

### [2] HttpSession

```
是由服务器提供的一种会话跟踪机制，安全性较cookie高，没有实体，不存在一个文件保存在客户端或者服务器，Session通过Request.getsession() 来进行创建，创建之后存在一个最大不活动周期默认是1800s(这个是可以修改的)之后，开始倒计时，如果在这期间内，用户提交了申请则倒计时重置，因此理论上Session是可以一直有效的，但是如果，关闭浏览器或者在最大不活动周期内没有请求提交，则Session销毁Session支持中文，通过setAttribute(String,Object)来封装信息
```

### [3] 如何销毁

#### [1] cookie

```java
1）生命周期到期
2）cookie.setMaxAge(0)--时销毁
```

#### [2] Session

```java
1）超过最大不活动周期(不同的计算机存在误差)
2）Session.invalidate()--这是立即销毁当前的Session
3）关闭浏览器--但是关闭浏览器并不是严格意义上的删除Session，而是当我们再次打开浏览器的时候会再次分配一个Session，之前的Session再也无法拿取
```

### [3] 他们之间的联系

```java
Session的传输是依靠cookie来进行维持的，cookie传输了Session的一个重要的参数Sessionid(这是唯一的Session的标识)
当用户书写Request.getSession()的时候，首先web容器首先会从cookie中查看是否当前存在Sessionid。如果，没有存在Sessionid，则创建一个新的Session，并分配给这个Session一个Sessionid，跳转之前将Sessionid封装进当前的cookie中
Cookie cookie = new Cookie("jsessionid","dfddfhs3423dhufs934");
当跳转到另一个页面中，当用户书写Request.getSession() 这个方法的时候，再次从cookie中拿取Sessionid，如果可以拿取，则根据拿取的Sessionid获得绑定的Session会话
```

### [4] Cookie禁用Session还能维持吗

```
如果想维持Session，可以使用重写URL的方法来继续维持Session的跟踪机制
```

# Jsp

## page指令元素

```
language:页面脚本类型,书写代码的种类
	1 import:页面导包，多个包用逗号隔开不推荐使用快捷键
	2 pageEncoding:页面转换编码，如果使用默认的iso-8859-1无法保存中文
	3 session:打开页面是否创建session支持session会话跟踪机制，如果为true，则支持
	4 buffer:页面缓存 默认8kb
	5 info:用来设置页面的签名 书写时间等信息，通过getServletInfo()方法可以取出
	6 isELIgnored:是否忽略EL表达式，false不忽略
	7 contentType:对应Servlet中response.setContentType("text/html;charset=utf-8")是软编码的一部分
	8 autoFlush:页面是否支持自动刷新，默认不支持
	9 isThreadSafe:当前页面线程是否安全默认true，不安全单实例多线程。更改为false 则底层实现			          	SingleThreadModel单实例单线程线程安全效率低下
	10 isErrorPage:当前页面能否使用exception内置对象true可以使用 false无法使用
	11 errorPage：当前页面如果出现异常，自动跳转到哪个页面
```

# EL表达式

### [1]表达式

凡是称之为表达式都是指仅仅可以显示数据的，无法进行复杂的业务逻辑处理。

#### [2]格式

**${要展示的内容}** 若大括号内存在引号 则直接输出

```java
<h3>算数运算符</h3>
  	 ${"3+1"}=${3+1}<br>
  	 ${"2-1"}=${2-1}<br>
  	 ${"5*3"}=${5*3}<br>
```

#### [3]范围取值

- 格式：${范围.key}     //key就是session.setArrtibute(name,key)

- 范围：pageScope requestScope sessionScope applicationScope

  在key值没有冲突的情况下，可以省略范围

  若不指定范围，默认取范围最小的

```java
${pageScope.elena }
${requestScope.elena }
${sessionScope.elena }
${applicationScope.elena }
```

#### [4]接收传值

- 接收页面传值

  ${param.etoak}

- 获取配置在web.xml中全局配置参数

  ${application.getInitParameter(key)}

#  Jstl

#### [1]取值 

 value要输出的值，可以使用java代码，可直接书写字符串 可以使用el表达式

```java
<c:out value="直接输出字符串"></c:out>
<% String str= "elena"; %>
<c:out value="<%=str %>"></c:out>
<c:out value="${pageScope.elena }"></c:out>
```

#### [2]删除值

var:要删除的key scope:要删除的范围  default:取不到值 默认显示

```java
<c:remove var="elena" scope="request"/>
<c:out value="${requestScope.elena }" default="删除成功"></c:out>
```


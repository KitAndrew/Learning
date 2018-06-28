# springmvc

## 执行顺序

1. 客户端发送请求
2. web.xml dispatcherServlet类解析[Servlet-name]-servlet.xml文件
3. 通过映射器找到@RequestMapping,匹配请求地址，找到对应的方法
4. 通过dispatcherServlet类找到适配器，由适配器执行与请求对应的方法
5. 接收参数请求，进行类型转换（因为接收的参数都是String类型）
6. 处理业务逻辑
7. 返回ModelAndView视图，交给dispatcherServlet类
8. dispatcher将视图交给视图解析器进行渲染，拼装前缀和后缀
9. 拿到渲染之后的完整路径，由View类进行相应给客户端

## 接收参数

1. 注解的方式

   @RequestParam（value="前端的name"） String username

   ```java
   	@RequestMapping(value="/login")
   	public ModelAndView login( 
   		@RequestParam(value="username") String username,
   		@RequestParam(value="password") String password){
   		System.out.println("用户名:"+username);
   		System.out.println("密码:"+password);
       }
   ```

   如果参数名与前端的name相同，就可以省略前面的@RequestParam()

2. 模型驱动

   ```java
   	@RequestMapping(value="/register",method=RequestMethod.POST)
   	public String register(Register r,HttpServletRequest request) {
   		System.out.println("用户名:"+r.getUsername());
   		System.out.println("密码:"+r.getPassword());
   		System.out.println("生日:"+r.getBirth());
   		System.out.println("性别:"+r.getGender());
   ```

3. springmvc同时集成了servlet,也可以这样写

   ```
   public String register(Register r,HttpServletRequest request) {
       	String username=request.getParameter("username");
   		String password=request.getParameter("password");
   		String birth=request.getParameter("birth");
   		String gender=request.getParameter("gender");
   }
   ```

1. HttpServlet 对象，主要包括 HttpServletRequest 、HttpServletResponse 和 HttpSession 对象。
   但是有一点需要注意的是在使用 HttpSession 对象的时候，如果此时 HttpSession 对象还没有建立起来的话就会有问题。
2. Spring 自己的 WebRequest 对象。
   使用该对象可以访问到存放在 HttpServletRequest 和 HttpSession 中的属性值。
3. InputStream 、OutputStream 、Reader 和 Writer 。
   InputStream 和 Reader 是针对 HttpServletRequest 而言的，可以从里面取数据；OutputStream 和 Writer 是针对 HttpServletResponse 而言的，可以往里面写数据。
4. 使用 @PathVariable 、@RequestParam 、@CookieValue 和 @RequestHeader 标记的参数。
5. 使用 @ModelAttribute 标记的参数。
6. java.util.Map 、Spring 封装的 Model 和 ModelMap 。
   这些都可以**用来封装模型数据**，用来给视图做展示。
7. 实体类。
   可以用来接收上传的参数。
8. Spring 封装的 MultipartFile 。
   用来接收上传文件的。
9. Spring 封装的 Errors 和 BindingResult 对象。
   这两个对象参数必须紧接在需要验证的实体对象参数之后，它里面包含了实体对象的验证结果。

## 返回视图方式

1. 使用ModelAndView视图类

   ``` java
   	ModelAndView mv = new ModelAndView("success");
   	//在xml中拼装
   	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
   		<!-- 视图前缀 -->
   		<property name="prefix" value="/"></property>
   		<!-- 视图后缀 -->
   		<property name="suffix" value=".jsp"></property>
   	</bean>
   	//或者是这样
   	ModelAndView mv = new ModelAndView();
   		mv.setViewName("success");
   ```

2. 也可以直接这样写

   return “视图名称”；

## springmvc返回类型

1. 一个包含模型和视图的 ModelAndView 对象。
2. 一个模型对象，这主要包括 Spring 封装好的 Model 和 ModelMap ，以及 java.util.Map ，当没有视图返回的时候视图名称将由 RequestToViewNameTranslator 来决定。
3. 一个 View 对象。
   这个时候如果在渲染视图的过程中模型的话就可以给处理器方法定义一个模型参数，然后在方法体里面往模型中添加值。
4. 一个 String 字符串。
   这往往代表的是一个视图名称。这个时候如果在渲染视图的过程中需要模型的话就可以给处理器方法一个模型参数，然后在方法体里面往模型中添加值就可以了。
5. 返回值是 void 。
   这种情况一般是我们直接把返回结果写到 HttpServletResponse 中了，如果没有写的话，那么Spring 将会利用 RequestToViewNameTranslator 来返回一个对应的视图名称。
   如果视图中需要模型的话，处理方法与返回字符串的情况相同。
6. 如果处理器方法被注解 @ResponseBody 标记的话，那么处理器方法的任何返回类型都会通过 HttpMessageConverters 转换之后写到 HttpServletResponse 中，而不会像上面的那些情况一样当做视图或者模型来处理。
7. 除以上几种情况之外的其他任何返回类型都会被当做模型中的一个属性来处理；
   而返回的视图还是由 RequestToViewNameTranslator 来决定，
   添加到模型中的属性名称可以在该方法上用 @ModelAttribute(“attributeName”) 来定义，否则将使用返回类型的类名称的首字母小写形式来表示。
   使用 @ModelAttribute 标记的方法会在 @RequestMapping 标记的方法执行之前执行。

## springmvc请求

springmvc只提供request session 两种状态

1. Model
2. ModelMap

可以把他们看成同一个对象，默认范围都是request

但是Model没有get方法，需要通过ModelMap来获取Model信息

将Model和ModelMap转换成session范围需使用注解，需要注意的是在获取sessIon中的对象的时候，也需要

@SessionAttributes(value={"xxx","xxx"})

返回视图时，springmvc提供请求转发状态码，和请求重定向状态码

```java
return "forward:/success.jsp";

return "redirect:/success.jsp";
```

使用状态码之后，视图解析器便不再对return或者ModelAndView生成的对象进行拼装。

**springmvc 只兼容2种请求格式**

<url-pattern>/</url-pattern>

1. *.do   *.action

禁止使用 *.html   不兼容json格式

2. 斜杠(/) 

   禁止使用/*
   使用/请求 表示springmvc集成restful
   使用/请求 默认不加载静态资源   js、css、<img>

## springmvc文件上传

1. 需要jar包

   - commons-fileupload-1.3.1.jar
   - commons-io-2.4.jar

2. 实例化上传解析器

   ```xml
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
   		<!--默认编码为 ISO-8859-1  -->
   		<property name="defaultEncoding" value="UTF-8"></property>
   		<!-- 设置上传大小 3m-5m -->
   		<property name="maxUploadSize" value="3145728"></property>
   </bean>
   ```

3. 代码示例

   ```java
   public String upload(
   			@RequestParam(value="f") MultipartFile f,
   			HttpSession session) {
   		
   		System.out.println("上传文件的类型：" + f.getContentType());
   		System.out.println("上传文件请求参数名称：" + f.getName());
   		System.out.println("上传文件名称：" + f.getOriginalFilename());
   		System.out.println("上传文件的大小" + f.getSize());
   		//true为空   false不为空
   		System.out.println("上传文件是否为空：" + f.isEmpty());
   		
   		//判断上传文件是否为空
   		if(f != null && !f.isEmpty() && f.getSize() > 0) {
   			
   			//获取上传文件保存路径
   			ServletContext sc = session.getServletContext();
   			String path = sc.getRealPath("/files");
   			
   			//获取文件路径
   			File file = new File(path);
   			
   			//判断路径是否存在
   			if(!file.exists()) {
   				//创建保存文件路径
   				file.mkdirs();
   			}
   			
   			//随机生成信息
   			String uuid = UUID.randomUUID().toString();
   			//获取上传文件后缀名称    et1803.txt   txt
   			String suffix = 
   					FilenameUtils.getExtension(f.getOriginalFilename());
   			
   			//组装上传文件名称
   			String fileName = uuid + "." + suffix;
   ```

   上传到指定路径保存有三种方式

   1. ```java
         try {
         				//将二进制信息上传到指定保存路径
         				f.transferTo(new File(path,fileName));
         			} catch (IllegalStateException | IOException e) {
         				e.printStackTrace();
         			}
         ```
      ```
   
   2. ```
      			try(
      				//获取上传二进制流信息
      				InputStream is = f.getInputStream();
      				//输出上传文件信息，并保存到指定路径
      				OutputStream out = 
      						new FileOutputStream(new File(path,fileName));
      			) {
      				//定义缓存信息   
      				//is.available()上传文件的大小  缓冲的长度
      				byte[] b = new byte[is.available()];
      				
      				//读取上传信息  等于-1表示读取到最后一个字节
      				while(is.read(b) != -1) {
      					//输出上传信息
      					out.write(b);
      				}
      			}catch (Exception e) {
      				e.printStackTrace();
      			}
      ```

   3. ```
         //获取上传二进制字节信息
         			//byte[] b = f.getBytes();
         			try(
         				
         				//输出上传文件信息，并保存到指定路径
         				OutputStream out = 
         						new FileOutputStream(new File(path,fileName));
         			) {
         					//输出上传信息
         					out.write(f.getInputStream(););
         			}catch (Exception e) {
         				e.printStackTrace();
         			}
         ```
      ```
   
      ```

4. 文件上传至ftp服务器

   ```java
   工具类 FtpUtils
   @RequestMapping(value="/uploadFtp")
   	public String uploadFtp(
   			@RequestParam(value="f") MultipartFile f) {
   		
   	    //通过ftp上传文件信息
   		//考虑压缩
   		FtpUtil ftp = FtpUtil.getInstance();
   		try {
   		//true上传成功  false上传失败
   		boolean b = ftp.upload(
   					"127.0.0.1", 21, 
   					"ay2853", "123456", 
   					"D:/ftp", //服务器保存上传文件路径
   					f.getOriginalFilename(), 
   					f.getInputStream());
   		
   		//判断上传是否成功
   		if(!b) {
   			System.out.println("上传失败");
   		}
   		
   		} catch (IOException e) {
   			e.printStackTrace();
   		}
   		
   		return "download";
   	}
   ```


## Filter处理中文乱码

```xml
  <!-- 处理乱码 post请求 start -->
  <filter>
  	<filter-name>encoding</filter-name>
  	<filter-class>
  		org.springframework.web.filter.CharacterEncodingFilter
  	</filter-class>
  	<!-- 请求 默认编码 iso-8859-1 -->
  	<init-param>
  		<param-name>encoding</param-name>
  		<param-value>UTF-8</param-value>
  	</init-param>
  	<!-- 响应 默认编码 iso-8859-1 -->
  	<init-param>
  		<param-name>forceEncoding</param-name>
  		<param-value>true</param-value>
  	</init-param>
  </filter>
  
  <filter-mapping>
  	<filter-name>encoding</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
  <!-- 处理乱码 post请求 end -->
```



## springmvc下载

下载原理：将响应头信息设置为

```
response.addHeader("Content-disposition","attachment;filename="+new String(originalFilename.getBytes("GB2312"),"ISO-8859-1"));
```

### 普通下载

```java
	@RequestMapping(value="/download")
	public void download(
			@RequestParam(value="fileName") String fileName,
			HttpSession session,
			HttpServletResponse response) {
		
		//获取下载附件路径
		ServletContext sc = session.getServletContext();
        String path = sc.getRealPath("/files");
		
		//获取下载附件原名称
		String originalFilename = (String)session.get("originalFilename");
		
		//定义响应头信息   针对下载
		try {
			response.addHeader("Content-disposition","attachment;filename="+
                               new String(originalFilename.getBytes("GB2312"),"ISO-8859-1"));
		} catch (UnsupportedEncodingException e1) {
			e1.printStackTrace();
		}
		try(
			//读取下载附件信息
			InputStream is = new FileInputStream(new File(path,fileName))
			//响应下载信息对象
		    OutputStream out = response.getOutputStream();
		) {
			
			//定义缓存信息
			byte[] b = new byte[is.available()];
			
			//读取下载附件信息，并输出响应给浏览器
            while(is.read(b)!=-1){
                out.write(b);
            }
			
		}catch(Exception e) {
			e.printStackTrace();
		}
		
	}
```

### ftp下载

```java
@RequestMapping(value="/downloadFtp")//使用了工具类FtpUtils
	public void downloadFtp(
			@RequestParam(value="fileName") String fileName,
			HttpServletResponse response) {
		
		//decoder解密
		String originalFilename = null;
		try {
			originalFilename = URLDecoder.decode(
					URLDecoder.decode(fileName, "UTF-8"),
					"UTF-8");
		} catch (UnsupportedEncodingException e2) {
			e2.printStackTrace();
		}
		
		//定义响应头信息   针对下载
		try {
			response.addHeader(
					"content-disposition", 
					"attachment;filename=" + 
					new String(originalFilename.getBytes("GB2312"),"ISO-8859-1"));
		} catch (UnsupportedEncodingException e1) {
			e1.printStackTrace();
		}
		
		try(
			//读取下载附件信息
			InputStream is = 
				FtpUtil.getInstance()
				.download(
						"127.0.0.1", 21,
						"ay2853", "123456", 
						"D:/ftp", originalFilename);
			//响应下载信息对象
		    OutputStream out = response.getOutputStream();
		) {
			
			//定义缓存信息
			byte[] b = new byte[is.available()];
			
			//读取下载附件信息，并输出响应给浏览器
			while(is.read(b) != -1) {
				out.write(b);
			}
			
		}catch(Exception e) {
			e.printStackTrace();
		}
		
	}
```

## URLEncoder,URLDecoder加密解密

一般都是加密2次，并解密两次

``` java
//encode加密
		String fileName = null;
		try {
			fileName = URLEncoder.encode(
					URLEncoder.encode(f.getOriginalFilename(), "UTF-8"),
					"UTF-8");
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}

		
//解密
		String originalFilename = null;
		try {
			originalFilename = URLDecoder.decode(
					URLDecoder.decode(fileName, "UTF-8"),
					"UTF-8");
		} catch (UnsupportedEncodingException e2) {
			e2.printStackTrace();
		}
```

## 与Struts2的区别

它们两者的实现机制是不同的，所以说区分是很大的，主要是下面几点：

- SpringMVC 的入口是 Servlet，Struts2 是基于 Filter，所以就说它们的实现机制是不同的
- SpringMVC 是基于方法的设计，也就是说传递参数是通过方法的形参，实现是单例（Spring 实例化对象默认就是单例），也推荐单例，这样就省去了创建、销毁对象的过程，提高效率，并且使用方法的形参传值，也不需要担心并发问题。
  Struts2 是基于类设计，传递参数通过类的属性，只能设置为多例。
- 参数传递方面，Struts2 因为是用类属性接收，所以不同方法可以共享；但是在 SpringMVC 中多个方法直接不能共享参数，因为是基于方法的嘛

## 输出&输入为JSON

- @RequestBody：
  如果一个方法申明了 @RequestBody 注解, Spring 会基于请求头中的 Content-Type 使用 HTTP Message converters 将 request body 反序列化成对象。
- @ResponseBody：
  是在方法的上注解，如果一个方法申明了@ResponseBody, Spring 会基于请求头中的 Accept 使用 HTTP Message converters 将对象序列化成 response body。
  在 Spring 4 中如果使用了 @RestController，则可以不用再声明此注解。

## 拦截器

- preHandle
  该方法在目标方法之前被调用（调用 Handler 之前），在该方法中对用户请求 request 进行处理
  若返回值为 true, 则继续调用后续的拦截器和目标方法.
  若返回值为 false, 则不会再调用后续的拦截器和目标方法.
  可以考虑做权限. 日志, 事务等。
  方法中的 Object 对象，对于静态资源是 ResourceHttpRequestHandler 类型（一般都会进行排除不会拦截的）；对于动态资源是 HandlerMethod 类型，通过它可以获得 Method 等对象。

- postHandle
  调用目标方法之后, 但渲染视图之前.
  可以对请求域中的属性或视图做出修改，也就是说：
  这个方法在业务处理器处理完请求后，但是 DispatcherServlet 向客户端返回响应前被调用，在该方法中对用户请求 request 进行处理。

- afterCompletion
  渲染视图之后被调用（在 DispatcherServlet 完全处理完请求后）. 可用来释放资源

- 需要配合在xml中配置拦截器

  ```xml
  	<mvc:interceptors>
  		<mvc:interceptor>
  		    <!-- 需要拦截的请求地址 -->
  			<!-- 	
  			<mvc:mapping path="/login/login" />
  			<mvc:mapping path="/login/logout" /> 
  			<mvc:mapping path="/login/*" />
  			-->
  			<mvc:mapping path="/**" />
  			<!-- 不需要拦截的请求地址 -->
  			<!-- <mvc:exclude-mapping path="/login/logout" /> -->
  			<mvc:exclude-mapping path="/js/**" />
  			
  			<!-- 
  				配置拦截器对象，并实例化该对象 
  				拦截器需要实现HandlerInterceptor接口
  			-->
  			<bean class="com.etoak.common.AuthInterceptor">
  			</bean>
  			<!-- <ref bean="authInterceptor" /> -->
  		</mvc:interceptor>
  	</mvc:interceptors>
  ```

  `/**` 是指所有目录，是指所有请求。 

  springmvc拦截器除了视图（jsp,html）不拦截，其他请求全部拦截，即使是静态资源也会拦截

## 路径匹配

在 SpringMVC 中遵循：路径匹配，先最长路径匹配，再最短路径匹配；优先级最高的精确匹配就不说了。
所以说 `/*` 可以匹配所有，如果设置为 `<url-pattern>/user/*</url-pattern>` 那么 `/abc/def/user/list` 也是可以匹配到的。

## 解决/默认不加载静态资源

1. ```
   <mvc:resources location="/js/" mapping="/js/**" />
   location 指定静态资源的位置，可以是 web application 根目录下、jar 包里；多个路径可使用 , 分割
   mapping 指的是 location 映射成的路径，前端请求访问的就是这个路径，** 表示的是包含多层目录。
   ```

2. ```
   <mvc:default-servlet-handler />
   将静态资源的处理经由 Spring MVC 框架交回Web应用服务器处理 。
   定义此标签后，会在 Spring MVC 上下文中定义一个 org.springframework.web.servlet.resource.DefaultServletHttpRequestHandler，它会像一个检查员，对进入 DispatcherServlet 的 URL 进行筛查，如果发现是静态资源的请求，就将该请求转由 Web 应用服务器默认的 Servlet 处理，如果不是静态资源的请求，才由 DispatcherServlet 继续处理。
   一般 Web 应用服务器默认的 Servlet 名称是 “default”，因此 DefaultServletHttpRequestHandler 可以找到它。如果你所有的 Web 应用服务器的默认 Servlet 名称不是 “default”，则需要通过 default-servlet-name 属性显示指定：
   <mvc:default-servlet-handler default-servlet-name="所使用的Web服务器默认使用的 Servlet 名称" />
   ```

## 单独跳转页面逻辑

使用**<mvc:view-controller path="" view-name=""/>** ，会触发视图解析器对view-name进行拼装。
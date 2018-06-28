# SSM整合

## 配置xml文件

### applicationContext.xml(spring)主容器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans  xmlns="http://www.springframework.org/schema/beans"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-4.0.xsd
		">
	<!-- spring 
		扫描IOC注解
		@Controller
		@Service @Repository
		@Autowired
	 -->
	<context:component-scan base-package="com.etoak"></context:component-scan>
	
	
	<!-- classpath等价与src -->
	<!-- <import resource="classpath:spring/spring-mybatis.xml"/> -->		
</beans>
```

### applicationContext-mybatis(spring-mybatis)主容器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
		">
	<!-- 引入資源文件properties -->
	<context:property-placeholder location="classpath:properties/jdbc.properties"
		file-encoding="UTF-8" />
	<!-- 1.配置数据源 默认没有事务 -->
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="${jdbc.driverClassName}"></property>
		<property name="url" value="${jdbc.url}"></property>
		<property name="username" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
	</bean>
	<!-- 2.配置mybatis -->
	<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 注入数据源 -->
		<property name="dataSource" ref="dataSource"></property>
		<!-- 实体类起别名 -->
		<property name="typeAliasesPackage" value="com.etoak.modules"></property>
		<!-- 加载mybatis sql配置文件 -->
		<property name="mapperLocations">
			<array>
				<value>
					classpath:com/etoak/**/mapper/*.xml
				</value>
			</array>
		</property>
	</bean>
	<!-- 3.扫描接口 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- 引入一个sqlSessionFactoryBean对象 获取sqlsession对象 -->
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"></property>
		<!--  
			扫描接口 通过代理模式创建对象，哪里需要 注入接口即可使用
			sqlsession.getMapper(接口);
		-->
		<property name="basePackage" value="com.etoak.**.mapper"></property>
		<!-- 扫描 接口时，接口加入注解才会被扫描，通过代理模式创建接口对象 -->
		<property name="annotationClass" value="org.springframework.stereotype.Repository"></property>
	</bean>
	<!-- 4.实例化事务管理器 -->
	<bean id="tx" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	<!-- 
		5.配置声明式事务 注解  使用cglib动态代理
	-->
	<tx:annotation-driven transaction-manager="tx" proxy-target-class="true"/>
</beans>
```

### springmvc.xml(springmvc)副容器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans  xmlns="http://www.springframework.org/schema/beans"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
		">
	<!-- springmvc 
		扫描IOC注解
		@Controller 实例化 服务端
		@Service @Repository
		@Autowired
	 -->
	<context:component-scan base-package="com.etoak" use-default-filters="false">
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/> 
	</context:component-scan>
	
	<!-- 映射器，适配器类型转换器 -->
	<mvc:annotation-driven />
	
	<!-- /请求不加载静态资源文件 -->
	<mvc:resources location="/skins/" mapping="/skins/**"/>
	
	<!-- 视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/pages/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
	
	<!-- springmvc拦截器 -->
	
	<!-- 上传解析器 -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<property name="defaultEncoding" value="UTF-8"></property>
		<property name="maxUploadSize" value="3000000"></property>
	</bean>
</beans>
```

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>SSM</display-name>
  <!-- spring监听器
   -->
  <listener>
  	<listener-class>
  		<!-- 默认加载spring配置文件路径为：
  			WEB-INF/applicationContext.xml -->
  		org.springframework.web.context.ContextLoaderListener
  	</listener-class>
  </listener>
  <!-- 自定义加载spring配置文件路径 -->
  <context-param>
  	<param-name>contextConfigLocation</param-name>
  	<param-value>
  		classpath:spring/applicationContext*.xml
  	</param-value>
  </context-param>
  
  <!-- springmvc配置  -->
  <servlet>
  	<servlet-name>mvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:spring/springmvc.xml</param-value>
  	</init-param>
  </servlet>
  <servlet-mapping>
  	<servlet-name>mvc</servlet-name>
  	<url-pattern>/</url-pattern>
  </servlet-mapping>
  
  <!-- 处理post乱码 -->
  <filter>
  	<filter-name>encoding</filter-name>
  	<filter-class>
  		org.springframework.web.filter.CharacterEncodingFilter
  	</filter-class>
  	<init-param>
  		<param-name>encoding</param-name>
  		<param-value>UTF-8</param-value>
  	</init-param>
  	<init-param>
  		<param-name>forceEncoding</param-name>
  		<param-value>true</param-value>
  	</init-param>
  </filter>
  <filter-mapping>
  	<filter-name>encoding</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```



## 事务问题

### spring+springmvc配置管理事务无效

> <https://blog.csdn.net/qq_32588349/article/details/52097943>

一般我们在 Spring 的配置文件 `applicationContext.xml` 中对 Service 层代码配置事务管理，可以对 Service 的方法进行 AOP 增强或事务处理如事务回滚；

但是遇到一个问题，在 Controller 类中调用 Service 层方法，配置的事务管理会失效，查询相关资料发现原因。

其实 Spring 和 SpringMVC 两个容器为父子关系，Spring 为父容器，而 SpringMVC 为子容器。

也就是说 `applicationContext.xml` 中应该负责扫描除 @Controller 的注解如 @Service，**而 SpringMVC 的配置文件应该只负责扫描 @Controller**，否则会产生重复扫描导致 Spring 容器中配置的事务失效。

当然如果使用的是 XML 配置 AOP 的方式来管理事务，那么应该是没有影响的。

## 解决方案 1

按照上面的规则，其实问题主要在于 SpringMVC 的配置文件扫包范围，Spring 的配置文件就算也扫了 @Controller 注解，事务管理的 Service 只要没被（SpringMVC）重新扫描就不会出现事务失效问题。

也就是说只要保证在 SpringMVC 的配置文件中只扫 controller 的包就可以了，如果分布在多个包结构下，可以使用逗号来配置多个。

## 解决方案 2

定义 SpringMVC 只扫描 @Controller 注解：

```
<context:component-scan base-package="com.nn.web.controller"  
                        use-default-filters="false">  
  <context:include-filter type="annotation"  
                          expression="org.springframework.stereotype.Controller" />  
</context:component-scan>  
```

这样其他注解就不会扫描了。

## 补充

在 `context:component-scan` 可以添加 **use-default-filters**，spring 配置中的 use-default-filters 用来指示**是否自动扫描**带有 @Component、@Repository、@Service 和 @Controller 的类。默认为 true，即默认扫描。

如果想要过滤其中这四个注解中的一个（不扫描），比如 @Repository，可以添加 `<context:exclude-filter />` 子标签：

```
<context:component-scan base-package="com.loli" scoped-proxy="targetClass" use-default-filters="true">  
  <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>  
</context:component-scan> 
```

上面的 `<context:include-filter/>` 子标签是用来添加扫描注解的。

但是如果添加和排除的是相同，**则必须 include-filter 在前，exclude-filter 在后**

> 引用：<https://blog.csdn.net/u014520797/article/details/50667720>

------

scoped-proxy 属性是指定代理，接受三个值：no（默认值），interfaces（接口代理），targetClass（类代理）

那什么时候需要用到 scope 代理呢，举个例子：

我们知道 Bean 的作用域 scope 有：singleton，prototype，request，session；

那有这么一种情况，当你把一个 session 或者 request 的 Bean 注入到 singleton 的 Bean 中时，因为 singleton 的 Bean 在容器启动时就会创建（假设为 A），而 session 的 Bean 在用户访问时才会创建（假设为 B），那么当 A 中要注入 B 时，有可能 B 还未创建，这个时候就会出问题；

那么用代理的时候就来了，B 如果是个接口，就用 interfaces 代理，是个类则用 targetClass 代理（这就是默认值 no 的情况下）
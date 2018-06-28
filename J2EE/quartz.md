# quartz

### 执行流程

1. 监听器监听触发器cron表达式。
2. 调度器调用触发器。
3. 触发器调用任务
4. 任务调用业务

### 业务实现方式

#### 实现Job接口

```java
//该类需要实现job接口，才能实现定时任务
public class Job1 implements Job{
	//execute执行业务逻辑
	//JobExecutionContext 定时的上下文对象 可以获取 任务等信息
	//上下文对象 可以理解成一个全局的参数 一旦创建上下文对象
	//在任务位置都可以获取上下文对象 类似HttpSession会话机制
	@Override
	public void execute(JobExecutionContext arg0) throws JobExecutionException {
		// TODO Auto-generated method stub
		System.out.println("job1:"+new Date());
	}
}
```

#### 自定义业务

```java
public class Job2{
	/*
	 * 自定义业务方法
	 * 不能定义方法参数
	 * 不能接收请求和返回响应
	 * */
	public void job() {
		System.out.println("自定义job:"+UUID.randomUUID().toString());
	}
}
```

### 配置文件

##### 未整合

```java
package com.etoak.test;

import org.quartz.CronTrigger;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerFactory;
import org.quartz.impl.StdSchedulerFactory;

import com.etoak.job.Job1;

public class Test {

	public static void main(String[] args) throws Exception {
		// TODO Auto-generated method stub
		//1 任务 任务调用业务
		//任务名称  spring 默认id属性别名
		//任务组名称  spring 默认default
		//任务执行业务操作
		JobDetail jobDetail = new JobDetail("job_1","g_job1",Job1.class);
		
		/**
		 * 2 触发器
		 * 
		 *触发器名称 名称唯一 spring 默认id属性别名
		 *触发器组名称  组名称唯一 spring 默认DEFAULT
		 */
		CronTrigger cron = new CronTrigger("trigger_1","g_trigger1");
		//核心 cron 表达式 设置在什么时间触发
		cron.setCronExpression("0/5 * * * * ?");
		
		//3 任务调度器 监听器
		SchedulerFactory sf = new StdSchedulerFactory();
		//Scheduler对象是调度器中核心对象
		//通过调度器工厂对象获取调度器对象
		Scheduler s = sf.getScheduler();
		
		//设置任务和触发器
		s.scheduleJob(jobDetail,cron);
		
		//启动调度器 开启监听触发器操作
		//调度器调用触发器 触发器调用任务 任务调用业务
		s.start();
	}

}
```

##### spring整合

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans  xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	
	<!-- 实例化业务对象 -->
	<bean id="job2" class="com.etoak.job.Job2" />
	
	<!-- 1 任务调用业务
		默认任务名称 id属性 别名
		默认任务组名称 DEFAULT
	 -->
	<bean id="jobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<!-- 引入业务对象 -->
		<property name="targetObject" ref="job2"></property>
		<!-- 调用业务方法 -->
		<property name="targetMethod" value="job"></property>
		<!-- 任务名称 -->
		<property name="name" value="job_2"></property>
		<!-- 任务组名称 -->
		<property name="group" value="g_job2"></property>
		<!-- 解决并发操作  true:异步操作 false:同步操作-->
		<property name="concurrent" value="false"></property>
	</bean>
	<!--2 触发器 触发器调用任务
		默认触发器名称 id属性 别名
		默认触发器组名称 DEFAULT -->
	<bean id="trigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
		<!--调用任务  -->
		<property name="jobDetail" ref="jobDetail"></property>
		<!-- cron表达式 设置触发时间 -->
		<property name="cronExpression" value="0/5 * * * * ?"></property> 
		<!-- 触发器名称 -->
		<property name="name" value="trigger_1"></property>
		<!-- 触发器组名称 -->
		<property name="group" value="g_trigger1"></property>
	</bean> 
	<!-- 3 调度器  监听器 
	实例化之后开启调度器(监听)
	-->
	<bean id="scheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<!-- 调用触发器 并监听触发cron时间表达式 -->
		<property name="triggers">
			<array>
				<ref bean="trigger"/>
			</array>
		</property> 
	</bean>
</beans>
```

### 注解实现任务调度

```java

@Component
public class Job3 {
	/*
	 * 自定义业务方法
	 * 不能定义方法参数
	 * 不能接收请求和返回响应
	 * */
	@Scheduled(cron="0/5 * * * * ?")
	public void job() {
		System.out.println("自定义job-注解:"+new Date());
	}
}
```

并在xml文件中配置：

```xml
<context:component-scan base-package="com.etoak"></context:component-scan>
	<!-- 扫描任务调度注解 -->
<task:annotation-driven proxy-target-class="true"/>
```

### quartz集群整合

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">

	<!-- 
	  	配置数据源  默认没有事务
	   -->
   <bean id="dataSource" 
         class="org.springframework.jdbc.datasource.DriverManagerDataSource">
   		<!-- 驱动 -->
   		<property name="driverClassName" 
   		               value="com.mysql.jdbc.Driver"></property>
   		<!-- 连接 -->
   		<property name="url" 
   		               value="jdbc:mysql://localhost:3306/quartz"></property>
   		<!-- 用户名 -->
   		<property name="username" 
   		               value="root"></property>
   		<!-- 密码 -->
   		<property name="password"
   		               value="mjylst"></property>
   </bean>
    <!-- 
   		配置事务管理器
    -->
    <bean id="tx" 
         class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    		<property name="dataSource" ref="dataSource"></property>
    </bean>

	<!-- 实例化  业务 -->
	
	<!-- 
		1.任务
	 -->													
	<bean id="jobDetail" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">  
        <property name="jobClass">  
            <value>com.etoak.job.MyQuartzJobBean</value>  
        </property>     
        <property name="durability" value="true" />     
        <property name="requestsRecovery" value="true" />       
    </bean>  
	
	<!-- 
		2.触发器
	 -->
	<bean id="cronTrigger" 
	      class="org.springframework.scheduling.quartz.CronTriggerBean">
			<!-- 
				触发器调用任务
			 -->
			<property name="jobDetail" 
						ref="jobDetail"></property>
						
			<!-- 设置cron时间表达式 -->			
			<property name="cronExpression" 
			              value="0/5 * * * * ?"></property>
			              
	</bean>
	
	<!-- 
		3.调度器  监听器
	 -->
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<!-- 
			调用多个触发器
		 -->
		<property name="triggers">
			<list>
				<ref bean="cronTrigger" />
			</list>
		</property>
		<!-- 注入数据源 -->
		<property name="dataSource" 
		           ref="dataSource"></property>
		<!-- 注入事务 -->
		<property name="transactionManager" 
		           ref="tx"></property>
		<!-- 把spring上下 文以key/value的方式存放在了quartz的上下文中了 -->
        <property name="applicationContextSchedulerContextKey" value="applicationContext" />
		<!-- 加载quartz配置文件 -->
		<property name="configLocation" value="classpath:quartz.properties" />   
	</bean>
	
	<!-- 
		处理spring日期格式  spring4.0
	 -->
	 <bean id="customEditorConfigurer" 
	       class="org.springframework.beans.factory.config.CustomEditorConfigurer">
	 	<property name="customEditors">
	 		<map>
	 			<entry key="java.util.Date" 
	 				   value="com.etoak.common.DateUtil"></entry>
	 		</map>
	 	</property>
	 </bean>
	 
</beans>
```



### Cron表达式详细用法

|   字段   |    允许值     | 允许的特殊字符  |
| :------: | :-----------: | :-------------: |
|    秒    |     0-59      |    ， *  / -    |
|    分    |     0-59      |    ， *  / -    |
|    时    |     0-23      |    ， *  / -    |
|   日期   |     1-31      | , - * ? / L W C |
|    月    | 1-12(JAN-DEC) |    ， *  / -    |
|   星期   | 1-7(SUN-SAT)  | , - * ? / L C # |
| 年(可选) |   1970-2099   |    ， *  / -    |

**格式为：秒 分 小时 日期 月份 星期 年**

例子：

0/5 * * * * ？：每5秒执行一次



**“*”字符被用来指定所有的值。**如："*"在分钟的字段域里表示“每分钟”。
**“?”字符只在日期域和星期域中使用。**它被用来指定“非明确的值”。当你需要通过在这两个域中的一个来指定一些东西的时候，它是有用的。
月份中的日期和星期中的日期这两个元素时互斥的一起应该通过设置一个问号来表明不想设置那个字段。星期跟日期“？”只能选一个

**“-”字符被用来指定一个范围。**如：“10-12”在小时域意味着“10点、11点、12点”。

**“,”字符被用来指定另外的值**。如：“MON,WED,FRI”在星期域里表示”星期一、星期三、星期五”。

**“/”字符用于指定增量。**如：“0/15”在秒域意思是每分钟的0，15，30和45秒。“5/15”在分钟域表示每小时的5，20，35和50。符号“*”在“/”前面（如：*/10）等价于0在“/”前面（如：0/10）。记住一条本质：表达式的每个数值域都是一个有最大值和最小值的集合，如：秒域和分钟域的集合是0-59，日期域是1-31，月份域是1-12。字符“/”可以帮助你在每个字符域中取相应的数值。如：“7/6”在月份域的时候只有当7月的时候才会触发，并不是表示每个6月。

**L是‘last’的省略写法可以表示day-of-month和day-of-week域，但在两个字段中的意思不同，例如day-of-month域中表示一个月的最后一天。**如果在day-of-week域表示‘7’或者‘SAT’，如果在day-of-week域中前面加上数字，它表示一个月的最后几天，例如‘6L’就表示一个月的最后一个星期五。

**字符“W”只允许日期域出现。**这个字符用于指定日期的最近工作日。例如：如果你在日期域中写 “15W”，表示：这个月15号最近的工作日。所以，如果15号是周六，则任务会在14号触发。如果15号是周日，则任务会在周一也就是16号触发。如果是在日期域填写“1W”即使1号是周六，那么任务也只会在下周一，也就是3号触发，“W”字符指定的最近工作日是不能够跨月份的。字符“W”只能配合一个单独的数值使用，不能够是一个数字段，如：1-15W是错误的。

**“L”和“W”可以在日期域中联合使用，LW表示这个月最后一周的工作日。**

**字符“#”只允许在星期域中出现。**这个字符用于指定本月的某某天。例如：“6#3”表示本月第三周的星期五（6表示星期五，3表示第三周）。“2#1”表示本月第一周的星期一。“4#5”表示第五周的星期三。

**字符“C”允许在日期域和星期域出现。**这个字符依靠一个指定的“日历”。也就是说这个表达式的值依赖于相关的“日历”的计算结果，如果没有“日历”关联，则等价于所有包含的“日历”。如：日期域是“5C”表示关联“日历”中第一天，或者这个月开始的第一天的后5天。星期域是“1C”表示关联“日历”中第一天，或者星期的第一天的后1天，也就是周日的后一天（周一）。
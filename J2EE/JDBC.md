# JDBC

##  1.创建实体类

- 封装属性
-  重写toString()
-  构造方法

##  2.创建工厂

- 书写xml连接文件
- 获取链接
  - 导包
  - 创建扫描器
  - 使用扫描器扫描xml文件并将其封装在document中
    - 获取根元素
    - 获取一级子元素，并遍历
      - 根据一级子元素属性判断选择的数据库链接属性(driver,url,user,pass)
      - 先通过反射得到数据库驱动(driver)
      - url
      - user
      - pass
      	 返回链接	return DriverManager.getConnection(url,user,pass)
  - 关闭链接

```java
//具体代码如下
public static Connection getCon(String dbType){
			try{
				SAXReader saxReader = new SAXReader();
				Document document = saxReader.read(new File("connection.xml"));
				Element root = document.getRootElement();
				List<Element> firstChild = root.elements();
				for(Element firstChildEle:firstChild){
					// 拿取一级子元素的id属性值
					String value=firstChildEle.attributeValue("id");
					if(value.equalsIgnoreCase(dbType)){
						// elementTextTrim 拿取标签内嵌套的值
						Class.forName(firstChildEle.elementTextTrim("driver"));
						String url = firstChildEle.elementTextTrim("url");
						String name = firstChildEle.elementTextTrim("username");
						String pass = firstChildEle.elementTextTrim("password");
						return DriverManager.getConnection(url,name,pass);
					}
				}
				return null;
			}catch(Exception e){
				e.printStackTrace();
				return null;
			}
		}
		public static void close(ResultSet rs,PreparedStatement pst,Connection con){
			try{
				if(rs!=null){
					rs.close();
				}
			}catch(Exception e){
				e.printStackTrace();
			}finally{
				try{
					if(pst!=null){
						pst.close();
					}
				}catch(Exception e){
					e.printStackTrace();
				}finally{
					try{
						if(con!=null){
							con.close();
						}
					}catch(Exception e){
						e.printStackTrace();
					}
				}
				
			}
		}
```



##  3.书写接口

##  4.实现类

- Connection con;

- prepareStatement pst;

- ResultSet rs;

- 通过工厂获取链接

   con = FactoryByXml.getCon("数据库类型");

- 定义sql

- 获取执行器

   pst = con.prepareStatement(sql);

   **pst.set类型（）** 

   setDate（int，java.sql.date） 如何将java.util.Date 转换成 java.sql.Date

   （java.sql.Date 是 java.util.Date的子类，java.sql.Date中只有日期 没有时分秒）

   new java.sql.Date（per.getBrith().DgetTime()） 			

- 执行器方法

    ​	.execute()			boolean
    	.executeUpdate()  	int
    	.executeQuery()		ResultSet

- 获取结果集

  ​	ResultSet  .next()  boolean

```java
//代码demo
public List<Person> getAll() {
		try{
			String sql = "select * from person";
			con = FactoryByXml.getCon("mysql");
			pst = con.prepareStatement(sql);
			rs = pst.executeQuery();
			List<Person> list = new ArrayList<>();
			while(rs.next()){
				Person per = new Person();
				per.setId(rs.getInt(1));
				per.setName(rs.getString(2));
				per.setPass(rs.getString(3));
				per.setAge(rs.getInt(4));
				per.setSalary(rs.getDouble(5));
				per.setBirth(rs.getDate(6));
				list.add(per);
			}
			return list;
		}catch(Exception e){
			e.printStackTrace();
			return null;
		}finally{
			FactoryByXml.close(rs, pst, con);
		}
	}
```


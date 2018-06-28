# Ajax

### 原生Js创建Ajax异步请求

#### [1]创建异步请求

```
var request = new XMLHttpRequest();
```

#### [2]发送异步请求，到指定目的地

```
request.open(method,url,async,username,password);
method:发送请求的类型(get或者post)
url:发送请求的目的地（若用get 可直接在后通过？跟传值）
async:默认为true表示使用异步请求，false为同步
Ex:
request.open("post","servlet/Check",true);
```

#### [3]设置使用字符流（post专属，get不需要书写此句）

```
request.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
```

#### [4]声明回调函数

```
这里表示在readyState参数发生更改时执行回调函数
request.onreadyStatechange = callback;
```

#### [5]设置要发送的值

```
get不需要从此处传值，如果不需要传递值填写null
request.send(null);
```

# Json

### key:必须为字符串

### value: 

1. 八种基本数据类型
2. 字符串
3. 自定义数据类型
4. 数组
5. 集合
6. null
7. json

### 格式1：{key:value,key:value,key:value***}

### 格式2：[value,value,value***]

### 封装方式1：

**可以封装任意数据类型，封装之后呈现格式1的json**

```
		int i = 10;
		boolean flag = true;
		String str = "etoak";
		String[] strAr = {"大明湖","趵突泉","千佛山"};
		
		JSONObject jo = new JSONObject();
		jo.put("key1",i);
		jo.put("key2",flag);
		jo.put("key3",str);
		jo.put("key4",strAr);
		System.out.println(jo);
		//{"key4":["大明湖","趵突泉","千佛山"],"key3":"etoak","key2":true,"key1":10}
```

### 封装方式2：

**专门用来封装自定义数据类型和Map**

```
Map<Integer,String> map = new HashMap<Integer,String>();
		map.put(1, "山东鲁能");
		map.put(2, "广州恒大");
		map.put(3, "上海上港");
		map.put(4, "北京国安");
		JSONObject js1 = JSONObject.fromObject(map);
		//{"3":"上海上港","2":"广州恒大","1":"山东鲁能","4":"北京国安"}
		System.out.println(js1);
		
JSONObject js2 = JSONObject.fromObject(new Game("堡垒之夜",98,"腾讯"));
		//{"total":98,"name":"堡垒之夜","seller":"腾讯"}
		System.out.println(js2);
		
		//对自定义数据类型进行有选择的封装
		JsonConfig jc = new JsonConfig();
		//设置不封装的属性名
		jc.setExcludes(new 
		String[]{"total","seller"});
		JSONObject js3 = JSONObject.fromObject(
		new Game("守望先锋",128,"暴雪"),jc);
		//{"name":"守望先锋"}
		System.out.println(js3);
```

### 封装方式3：

**用来封装数组 List 和 Set，封装之后呈现格式2的json**

```
JSONArray ja = JSONArray.fromObject(strAr);
		//["大明湖","趵突泉","千佛山"]
		System.out.println(ja);
		
		List<String> list = new ArrayList<String>();
		list.add("Java");
		list.add("C");
		list.add("C++");
		list.add("Php");
		JSONArray ja2 = JSONArray.fromObject(list);
		//["Java","C","C++","Php"]
		System.out.println(ja2);
		
		Set<String> set = new HashSet<String>();
		set.add("济南");
		set.add("青岛");
		set.add("淄博");
		set.add("济宁");
		JSONArray ja3 = JSONArray.fromObject(set);
		//["青岛","济宁","淄博","济南"]
		System.out.println(ja3);
```




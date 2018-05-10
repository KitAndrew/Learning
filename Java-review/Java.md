#  Java

## 1. 面向对象的三大特征

  -   封装（Encapsulation）

        - 封装对于类的开发者和使用者都有好处：
            - 开发者: 能够让数据得到保护 从而更加安全
            - 使用者: 能够让使用者将精力集中在核心业务上
      - 属性：
        - 官方要求 每一个属性都应当使用private修饰 
      - 方法：
        - 学会分辨方法是核心方法 还是 给核心方法提供服务的方法
        - 核心方法使用public修饰，给核心方法服务的方法 用private修饰

  -   继承（Inheritance）

        - 父类有相当于子类也有
            - **继承是代码最有效的共享方式之一**
      - Java类只支持单继承
      - 私有属性到底算不算继承
        - 私有属性在子类的存储空间中确实存在，但是这是因为创建对象的流程导致的，但是不能直接使用了，所以不能算继承得到的。

  -   多态（Polymorphism）

        -   **一个事物总有多种存在的形态，一个对象总有多种称呼它的类型**

              -   父类引用指向子类对象

              -   编译的时候，编译器会根据对象左侧的类型来进行语法检查，但是运行的时候，**事物时客观存在的，不会因为你改变称呼它的类型而发生任何变化**，So，我们调用方法的时候，什么类型的对象定然执行什么类的方法。（若左侧的类中没有下面调用的方法，即使右侧的类中有这个方法，在编译时，编译器会报错）

                  **编译看左，运行看右**

            - 多态能够降低类和类之间的耦合关系，让先出现的类型，再不做任何修改的情况下就能跟之后出现的新类型结合起来使用。

## 2.方法重载

 - 发生在同一类中
 - 方法名相同
 - 参数列表不同的两个方法
    - 参数个数不同
    - 参数类型不同
    - 参数顺序不同
- **能够让同一个方法有多种不同的存在形态，从而让方法使用方位更加广泛**

## 3.方法覆盖（方法重写）

- **发生在有继承关系的父类和子类之间，子类当中，子类对父类的方法重新实现**

- 越变越好

  - 访问控制权限修饰符不能越变越小
  - 方法签名 = 返回值类型 + 方法名 + 参数列表  （必须完全相同，Jdk5.0之后支持**协变**）

  |    public    | void  test（int x） | throws Exception{} |
  | :----------: | :-----------------: | :----------------: |
  | 不能越变越小 |       不能变        |    不能越变越大    |

- 如果在方法覆盖的时候，需要沿用父类的方法实现，可以使用super.方法名()；

- JDK5.0之后 可以用注解来保证方法覆盖必然成立

- JDK5.0之后 支持协变返回类型（既子类覆盖父类的返回值类型（有返回值，既子类的父类的子类返回值类型）可以改变，）

## 4.构造方法

- **是在创建对象时需要调用的方法（并不是它创建的对象）**
  - 为何要使用构造方法
    - 能够在创建对象的同时直接完成对属性的赋值操作
  - 构造方法与众不同的地方
    - 没有返回值类型，void都没有
    - 通常与类名相同，So，首字母需要大写
  - Java类中都有构造方法
    - 默认提供无参构造方法
    - 但是当 我们自己写了构造方法后，无参构造方法便会消失，若想使用需自己添加
  - 构造方法的首行，要么是this（），要么是super（）
    - 构造方法的首行，默认是super（）
      - super（）代表在执行当前构造方法之前会执行父类的构造方法，且默认会找无参空体的。若父类中没有无参构造方法则会编译报错，解决方案：
        - 手动写出父类的无参构造方法
        - super（）中若有参数，便会根据参数类型在父类中寻找相应的构造方法执行。
    - 构造方法的首行，还可能是this（）
      - this（）代表执行当前构造方法之前，先去执行本类中的其他构造方法，具体执行哪一个要看（）中的参数类型。
  - 构造方法可以重载，但是不能重写（因为他是不能被继承的，**构造方法的名字与类名相同！**）

## 5.参数传递

1. 基本类型传值的时候相当于把值直接复制了一遍，在栈内存中存储。
2. 引用数据类型传引用的值，而引用的的值就是一个内存指向的地址值。
3. **方法的参数相当于局部变量，随着方法的执行结束之后消亡**。

```java
public class Args{
    public static void main(){
        int number = 2;
        change(number);
        System.out.println(number);
        Int ok = new Int(number);
        change(ok);
        System.out.println(ok.i);
        changeRef(ok);
        System.out.println(ok.i);
    }
    public static void change(int x){
        x = 5;
    }
    public static void change(Int a){
        a.i = 3;
    }
    public static void changeRef(Int a){
        a = new Int(7);
    }
}
class Int(){
    int i;
    public Int(int i){
        this.i = i;
    }
}

```

## 6.字符串

- 字符串其实就是char类型的数组
	 用String  a =	"" 直接赋的值，会现在常量池中保存，再次赋值时，会先在常量池中过滤，若已经出现过，则不会再分配新的空间，直接指向原有空间。
- new 新对象时，不涉及常量池。
- String
  - 不会预留任何缓冲空间。在进行追加操作时，都需要开辟新空间，转移老元素，改变引用指向，回收旧空间，效率低。
- StringBuffer
  - 此层预留缓冲空间。
  - append（）在缓冲区末尾追加内容，insert（x,y）在x下标位置下插入内容，reverse（）反转整个字符串。
  - 单线程，线程安全，不会出现并发错误。
- StringBuilder
  - 比StringBuffer效率还高，支持多线程，效率更高，但可能会出现并发错误。

## 7.String类方法

- 和长度有关

  - **length（）**

    - 返回字符串的长度。

    - ```java
      public static void main(String[] args){
      	String str = "jsdalfkjsdlfkj";
      	int length = str.length();
      	System.out.println(length);
      }
      ```

- 和数组有关

  ```JAVA
  // toCharArray()  将字符串转为char类型的数组
  public static void testToCharArray(){
  	String str = "jsdalfkjsdlfkj";
  	char[] data = str.toCharArray();
  	//System.out.println(str.toCharArray()[0]);
  	for(char a : data){
  		System.out.println(a);
  	}
  }
  //getBytes() 将字符串转为字节数组  byte[]  可用来判断字符串中有几个汉字（一个英文字母，数字为一个字节）
  public static void testGetBytes(){
  	String str = "jsdalfkjsdlfkj";
  	byte[] a = str.getBytes();
  	System.out.println(a.length);
  }
  //charAt(int index(索引下标))  返回指定下标位置的char类型值
  public static void testCharAt(){
  	String str = "jsdalfkjsdlfkj";
  	char a = str.charAt(5);
  	System.out.println(a);
  }
  //split(String content) 此问题可理解为这个字符串中有几个"f",遍历出 n 个元素，就有 n - 1 个 f
  //以指定的内容分割字符串
  public static void testSplit(){
  	String str = "jsdalfkjsdlfkj";
  	String[] data = str.split("f");
  	for(String a : data){
  		System.out.println(a);
  	}
  }
  ```

- 和判断内容有关，返回值类型为true/false

  ```java
  //equals(String content) 比较两个字符串的内容是否相同。 返回true或false
  //equalsiIgnoreCase(String content)  比较两个字符串的内容是否相同，忽略大小写  返回true或false
  public static void testEquals(){
  	String str  = "jsdalfkjsdlfkj";
  	String str1 = "jsdalFkjsDlfkj";
  	System.out.println(str.equalsIgnoreCase(str1));
  }
  //contains(String content) 判断字符串中是否包含指定内容 返回true或false
  public static void testContains(){
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.contains("dal"));
  }
  //startsWith(String content) 是否以指定的内容开始 
  //endsWith(String content)  是否以指定的内容结尾
  public static void testStartsWith(){
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.startsWith("jsd"));
  	System.out.println(str.endsWith("fkj"));
  }
  ```

- 和处理内容有关，**以下所有方法并未改变原字符串**

  ```java
  //toUpperCase() 将字符串的小写字母转为大写字母
  //toLowerCase() 将字符串的大写字母转为小写字母
  public static void testToUpperCase(){
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.toUpperCase());
  }
  	
  //replace(String content,String content)  以指定的内容替换字符串   不支持正则
  //replaceAll(String content,String content)	 以指定的内容替换字符串 支持正则
  //replaceFirst(String content,String content)	以指定的内容替换正则中第一个字符
  public static void testReplace(){
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.replace("j","y"));
  	System.out.println(str.replaceAll("[a-z]",""));
  	System.out.println(str.replaceFirst("j","i"));
  }
  //substring(int index)  以指定的位置截取到最后
  //substring(int index,int index)  	截取两个指定位置的字符串  能取到头，但取不到尾
  public static void testSubstring(){
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.substring(5));
  	System.out.println(str.substring(5,9));
  }
  //trim() 去除字符串开头跟结尾的空白
  public static void testTrim(){
  	String str  = "  jsdalfkjsdlfkj  "; 
  	System.out.println(str.trim());
  }
  ```

- 查找位置

  ```java
  //indexOf(String content)   查找指定内容第一次在字符串中的索引下标位置  常与substring连用
  //lastIndexOf(String content,int index) 在指定的范围内，反向查找指定内容最后一次在字符串中的索引下标位置
  public static void testIndexOf(){ 
  	String str  = "jsdalfkjsdlfkj";
  	System.out.println(str.indexOf("d"));
  	System.out.println(str.lastIndexOf("d",12));
  }
  ```

- 类型转换

  ```java
  //valueOf()  各种类型转化为字符串  String a = 89 + "";
  public static void testValueOf(){
  	Integer a = 67;
  	String b = String.valueOf(a);
  	System.out.println(b);
  }
  ```


## 7.Java修饰符

- 访问权限控制修饰符（权限从小到大）

  - private

    - 私有的，只有本类可以调用

  - default

    - 默认的，本包内可以调用

  - protected

    - 受保护的，本包內可以调用，包外有继承关系的**子类中**（子类只能调用自己继承的方法）可以调用

  - public

    - 公共的，任何位置都可以调用

  - |  修饰符   |  类   | 成员 |
    | :-------: | :---: | :--: |
    |  private  | false | true |
    |  default  | true  | true |
    | protected | false | true |
    |  public   | true  | true |

- Final 修饰符  最终的

  - 修饰类
    - 类为最终类，**不能有派生类（子类）**，常见此类  String  Math
  - 修饰方法
    - 最终方法，**不能被覆盖。可以被继承**。
  - 修饰变量
    - 最终变量，一旦赋值不能更改。
      - **修饰基本类型数据，值不能修改**
      - **修饰引用类型数据，指向的地址不能修改，值可以更改**。

- statci 静态的 

  - 修饰属性

    - 静态属性在内存中只有一份值，不依赖对象，类中共享。
    - 访问静态属性，直接类名.属性名即可。
    - 静态属性与对象无关，在类被加载的时候初始化。

  - 修饰方法

    - **静态方法直接用 类名.方法名 调用**，**非静态方法必须用 对象名.方法名 调用** 例：System.arraycopy() , Arrays.sort()  System.currentTimeMillis(),String.valueOf()
    - **静态方法不能直接访问非静态变量（可直接访问静态变量）**
      - 静态方法在调用的时候是不需要对象而是用类名直接调用，对象都没有，哪来属性。

  - 修饰代码块

    - **普通初始化块，用于初始化非静态的属性，每创建一次对象初始化一次。**

    - **静态初始化块，用于初始化静态属性，静态属性只有一份，只需要初始化一次，跟创建几个对象没关系，静态初始化块在类被第一次加载的时候执行。**

    - 构造方法，普通初始化代码块，静态初始化块执行顺序先后

      - 在类第一次被加载时，静态初始化代码块先执行且只执行一次。

      - 再执行初始化代码块

      - 最后执行构造方法（构造方法是在创建对象的最后调用的方法给属性赋值。）

        - ```java
          class Person{
           private String name ;
           private int age ;

           public Person(String name , int age){
            this.name = name ;
            this.age = age ;
           }
          }
          public class Test{
              public static void main(String[] args){
                  Person p = new Person("大哥"，23);
              }
          }
          ```

## 8.单例 + abstract

- 单例模式

  - 一个类只有一个实例，最大限度的节约资源开支。

    - 单例模式三步走
      - 私有化构造方法，防止类之外随意创建对象。
      - 本类当中创建个当前类型的对象（为了防止无限创建，应用static修饰；为了防止恶意修改，应用private修饰）
      - 提供一个获取对象的getter方法（应该是静态的，必须是不需要对象就能调用）

  - 单例模式又分醉汉式，懒汉式

    - 醉汉式

      - 提前在内存中创建好对象，在用户调用时，直接赋值。

    - 懒汉式

      - 当用户使用时再创建对象。

        ```java
        //醉汉式
        class Sun{
        	private Sun(){}
        	private static Sun Only = new Sun();
        	public static Sun getOnly(){
        		return Only;
        	} 
        }
        //懒汉式
        class Cup{
        	private Cup(){}
        	private static Cup cup;
        	public static Cup getCup(){
        		if(cup == null)
        			cup = new Cup();
        		return cup;
        	}
        } 
        ```

- abstract  抽象的，不具体的

  - 修饰方法
    - 抽象方法，描述当前类型一定有这个方法，但是眼下给不出具体实现，具体实现留待子类当中完成
    - 抽象方法不允许有方法体，直接以 ; 结束。
  - 抽象类
    - 这个类描述不形象，不具体，不创建对象
  - 一个类只要有抽象方法，这个类就是抽象类
    - 抽象类可以没有抽象方法，但有抽象方法一定是抽象类。
  - 抽象类是类么？抽象类有构造方法么？
    - 是类。有构造方法，留给子类来覆盖。

## 9.Interface

- 接口中定义的变量，默认添加：public static final  也就是常量
- 接口中定义的方法，自动添加：public abstract
- 接口优点：
  - 有助于解除类与类之间的耦合关系
  - 有助于解决人与人之间的耦合关系
- 类不能继承借口，只能实现接口。 接口不能实现接口，接口可以继承接口，且支持多继承，一个类可以在继承一个类的情况下实现多个接口。
- **JDK8.0之后接口中有两种情况是可以出现方法体的**
  - static修饰的静态方法（接口名.方法名）
  - 返回值前面+default修饰的非静态方法，可在子类中调用，也可在子类中重写。
- **接口与抽象类的区别**
  - 抽象类中定义的变量，代表每个这个类型的对象都有这样的属性
  - 接口中定义的变量，自动添加：public static final 相当于变量
  - 抽象类中定义的方法可以为抽象的，可以是具体的
  - 接口中定义的方法自动添加：public abstract   JDK8.0之前必须是抽象方法​

## 10.Object

- clone（）
  - 用public重写Object类的clone方法
  - 返回值类型可以协变为当前类型
  - 方法签名最后 应由throws CloneNotSupportedException
  - 方法体内调用父类super.clone() 完成克隆
  - 当前类必须实现Cloneable接口
  - 调用的方法也要抛异常
- finalize（）
  - 对象在被GC回收之前调用的方法
  - 主动调用GC     System.gc();
- toString（）
  - 将对象转换为字符串
- equals（）  主要是覆盖equals（）
  - 判断参数是否为null 若为null返回false
  - 判断参数是否时当前类型（对象 instanceof  类型）若不是直接返回false
  - 判断参数是否 == 当前对象 若是 直接返回true
  - 找到两个参与比较的对象
  - 按需求比较属性
- hashcode（）
  - 生成一个对象的散列码。
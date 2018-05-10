### Stream

流：数据从原点传输到汇点的管道。

流的三种分类：

​			方向分：输入流	输出流

​			单位分：字节流	字符流

​			功能分：字节流	过滤流（包装流，处理流）

InputStream			所有字节输入流统一的父类		抽象类

​			int read()

​			int read(byte[] data)

​			int read(byte[] data,int off,int len)

OutputStream		所有字节输入流统一的父类		抽象类

​			write(int data)

​			write(byte[] data)

​			write(byte[] data,int off,int len)

FileInputStream			输入流	字节流	节点流

FileOutputStream		输出流	字节流	节点流

​	*：作为节点流，构造方法可以指定连接String文件名 File对象

​	*：虽然贵为节点流 但是他们只能连接文件 不能连接目录

​	*：请注意 节点输出流是有杀伤性的

​		    节点输出流连接的文件 即便不存在

​		     在创建流的时候也会被自动创建出来

​		     其实File类有个createNewFile()没用

​		     但是如果连接的目录结构都不再存在 则直接异常

​		     File类还有个mkdirs() 				--等优先级

​	*：节点输出流连接的文件 即便已经存在

​		     在创建流的一刻 也会被新的空白文件直接替换

​		     如果我们的需求是想要在最后追加内容

​		     那么构造方法：new FileOutputStream("文件名",true)；

​		     true:代表追加模式开启

​	*：FileInputStream 最常见的是read(byte[] data)

​	*：FileOutputStream最常用的却write(byte[] ,int ,int)

​	*：FileInputStream 以-1作为读取结束的标识。

BufferedInputStream			输入流	字节流	过滤流

BufferedOutputStream		输出流	字节流	过滤流

​	*：作为过滤流的他们是为了给原本的节点流添加缓冲空间,从而提高每次读写的吞吐量，进而提高效率

​	*：它们都是过滤流 不能连接文件 只能连接其他的流

​	*：它们构造方法的第二个参数可以指定缓冲空间大小

​	      默认为8192字节  8K

​	*：一定记得及时清空缓冲空间 防止数据滞留缓冲区

​			1. 满了自动清理

​			2.关闭流的操作会触发清空缓冲

​			3.手动调用flush()

DataInputStream

DataOutputStream

​	*：作为过滤流给原本的节点流添加读写基本数据类型的功能

​	*：DataInputStream 提供了一系列 readXxxx();

​	*：DataOutputStream 提供了一系列 writeXxxx();

​	*：DataInputStream 不再以-1作为读取结束的标识

​			如果已经到达文件结尾还继续读取,则直接报EOFException(End Of File)

ObjectInputStream

ObjectOutputStream

​	*：作为过滤流的它们是为了给原本节点流添加读写对象的功能的

​	*：ObjectInputStream提供readObject();

​	*：ObjectOutputStream提供writeObject();

​	*：ObjectInputStream同样不以-1作为结束 读写到最后会报EOFException

​	*：想要持久化必须要先序列化 implements Serializable

​			如果想要持久化的UI想当中有其它引用类型的属性

​			就连属性的类型也要实现序列化接口

​			但是如果某些属性无关紧要，不需要参与序列化，可以用 transient 关键字修饰

​			如果要持久化的是一个集合对象

​			则要求集合当中的元素类型也必须实现序列化接口

​			如果要持久化的是一个使用了比较器的TreeSet或者TreeMap,连比较器的类型也要实现序列化接口
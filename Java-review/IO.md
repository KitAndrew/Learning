# IO

### 构造方法

1. new File（String  完整路径）；
2. new File（String 父目录，String 文件名）；
3. new  File（File 父目录对象，String 文件名）；

### 常用方法：

1. static listRoots（）：得到当前计算机的所有根目录；

2. String[]    list（）：动态列出一个目录中所有文件的名字以String[] 返回；

3. File[]  listFiles（）：动态列出一个目录中的所有文件对象；

4. exits（）：判断File对象所指文件或目录是否存在

5. isFile（） isDirectory（）：判断File对象是文件还是目录；

6. length（）：返回文件字节数；

7. mkdirs（）：创建File对象所指文件或目录；

8. renameTo（必须为File对象）：a.renameTo(c)  a代表源文件  c代表目标文件   //c 与 a 可以不在同一目录实现剪切。

9. delete（）：删除文件或目录，目录必须为空；

10. getName（） ：获取文件或目录名字；

11. getParent（）：获取文件或目录的父目录；

12. getAbsolutePath（）：获取文件或目录的绝对路径；

13. setLastModified（）：设置文件最后修改的时间；

14. lastModified（）：获取文件最后修改时间；

    1. 如何解析日期和时间

       1. java.util.date;

          getYear() getMonth()+1 getDate()

          getHours() getMinutes() getSeconds()

       2. java.util.Calendar:

          get(1)  get(2)+1 get(5)
          get(11) get(12) get(13) 

       3. java.text.SimpleDateFormat    SingleDateFormat f = new SingleDateFormat("yyyy-MM-dd  HH-mm-ss");

          format（）从long类型到String

          parse（）从String到Date再到long（getTime（））

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​

          ​
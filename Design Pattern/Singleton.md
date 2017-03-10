#单例模式
###懒汉式
1. 自己构造方法设置为private，其他人不能new实例
2. 提供一个static方法给别人获取实例
```java
public class FileIO {
    private static FileIO fileIO;
    private FileIO() {
        
    }
    //懒汉式非线程安全，若要线程安全可以加上synchronized
    public static FileIO getInstance() {
        if (fileIO == null) {
            fileIO = new FileIO();
        }
        return fileIO;
    }
}
//FileIO.getInstance().openFile(fileName);//获取FileIO实例
//在需要的时候才创建，谓之“懒”，更流行方便
```
###饿汉式
```java
public class HttpIO {
    private static final HttpIO INSTANCE = new HttpIO();
    private HttpIO() {
        
    }
    //此时不能传入参数尽量辅助构造方法进行初始化
    public static HttpIO getInstance() {
        return INSTANCE;
    }
    //而且如果只想获取一个全局变量的时候，类加载的时候还会对fileIO实例进行初始化
    //可以使用静态内部类
    /*
    private static final class FileIOHolder {
        private static final FileIO INSTANCE = new FileIO();
    }
    */
}
//一上来就创建对象，谓之“饿汉”
```
###线程安全
懒汉式不安全：
线程一： FileIO.getInstance()
（FileIO:判断fileIO为null，进行fileIO实例的初始化）

线程二： FileIO.getInstance()
(FileIO:  fileIO还没初始化完，依然为null, 于是进行另外一个fileIO实例的初始化)
###优化
```java
public class FileIO {
    private static volatile FileIO fileIO;
    private FileIO() {
        
    }
    //懒汉式非线程安全，若要线程安全可以加上synchronized
    public static FileIO getInstance() {
        //如果非空，直接和synchronized无关
        if (fileIO == null) {
            synchronized (FileIO.class) {
                //二次检验
                if (fileIO == null) {
                    fileIO = new FileIO();
                 }
            }
        }
        return fileIO;
    }
}
//双重检验
/*
* 为什么使用volatile？
* 因为代码优化的时候可能出现代码重排，因此在new实例的时候有三个流程：
* 1、在堆空间里分配一部分空间；
  2、执行FileIO的构造方法进行初始化；
  3、把fileIO对象指向在堆空间里分配好的空间。
  但是优化的顺序肯呢个是1-3-2
  执行完3的时候就fileIO对象就已经不为空了，如果是按照1-3-2的顺序执行，恰巧在执行到3的时候（还没执行2），突然跑来了一个线程，
  进来getInstance()方法之后判断fileIO不为空就返回了fileIO实例。
  而fileIO实例虽不为空，但是还没执行构造方法进行初始化，而若需要初始化，则会报错。
  */
```
###枚举类型
反编译克制枚举实际上就是一个集成了Enum的类，而枚举的特点是只会有一个实例，同时保证了
线程安全、反射安全和反序列化安全。

##参考《码农翻身》中文章！！


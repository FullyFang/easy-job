#ThreadLocal
###ThreadLocal是什么
ThreadLocal是一个关于创建线程局部变量的类。

其实它就是一个容器，用于存放 “线程的局部变量”，应该称之为ThreadLocalVariable。

通常我们创建的变量是可以被任何一个线程访问并修改得到。
而使用ThreadLocal创建的变量只能被当前线程访问，其他线程无法访问和修改。
###Global && Local
* Global的意思是在当前线程中，任何一个点都可以访问到ThreadLocal的值。
* Local意思是该线程的ThreadLocal只能被该线程访问，一般情况下其他线程访问不到。
###用法简介
```java
private ThreadLocal myThreadLocal=new ThreadLocal();
```
实例化对象一次，并且也不需要知道它是被哪个线程实例化，虽然所有的线程都能访问到这个ThreadLocal实例，
但是每个线程却只能访问到自己通过调用ThreadLocal的set()方法设置的值。即使是两个不同的线程在同一个
ThreadLocal对象设置了不同的值，依然无法访问到对方的值。
####访问ThreadLocal变量
一旦创建了一个ThreadLocal的变量，你可以通过如下代码设置某个需要保存的值：
```java
myThreadLocal.set("a Thread local value");
```
可以通过下面方法读取保存在ThreadLocal变量中的值
```java
String threadLocalValue=(String)myThreadLocal.get();
```
####为ThreadLocal指定泛型类型
```java
private ThreadLocal muThreadLocal=new ThreadLocal<String>();
```
####如何初始化ThreadLocal变量的值
由于在ThreadLocal对象中设置的值只能被设置这个值得线程访问到，
线程无法在ThreadLocal对象使用set()方法保存一个初始值，并且这个初始值能被所有的线程访问到

我们可以通过创建一个ThreadLocal的子类并且重写initialValue()方法，
来为一个ThreadLocal对象指定一个初始值

```java
private ThreadLocal myThreadLocal=new ThreadLocal<String>(){
        @Override
        protected String initialValue(){
            return "this is the initial value"
      }
};
```
###源码实现
#### set方法
* 首先获取当前线程
* 利用当前线程作为句柄获取一个ThreadLocalMap对象
* 如果上述ThreadLocalMap对象不为空，则设置值，否则创建这个ThreadLocalMap对象并设置值
```java
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}

class Thread implements Runnable {
    /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */

    ThreadLocal.ThreadLocalMap threadLocals = null;
}
```
###对象存放在哪里
在Java中，栈内存归属于单个线程，每个线程都会有一个栈内存，其存储的变量只能在其所属线程中可见，即栈内存可以理解成线程的私有内存。
而堆内存中的对象对所有线程可见。堆内存中的对象可以被所有线程访问。

那么是不是说ThreadLocal的实例以及其值存放在栈上呢？

其实不是，因为ThreadLocal实例实际上也是被其创建的类持有（更顶端应该是被线程持有）。
而ThreadLocal的值其实也是被线程实例持有。

**它们都是位于堆上，只是通过一些技巧将可见性修改成了线程可见。**

###真的只能被一个线程访问么
既然上面提到了ThreadLocal只对当前线程可见，是不是说ThreadLocal的值只能被一个线程访问呢？

使用InheritableThreadLocal可以实现多个线程访问ThreadLocal的值。

如下，我们在主线程中创建一个InheritableThreadLocal的实例，
然后在子线程中得到这个InheritableThreadLocal实例设置的值。

```java
private void testInheritableThreadLocal() {
    final ThreadLocal threadLocal = new InheritableThreadLocal();
    threadLocal.set("droidyue.com");
    Thread t = new Thread() {
        @Override
        public void run() {
            super.run();
            Log.i(LOGTAG, "testInheritableThreadLocal =" + threadLocal.get());
        }
    };

    t.start();
}
```
###内存泄露
有网上讨论说ThreadLocal会导致内存泄露，原因如下

* 首先ThreadLocal实例被线程的ThreadLocalMap实例持有，也可以看成被线程持有。
* 如果应用使用了线程池，那么之前的线程实例处理完之后出于复用的目的依然存活
* 所以，ThreadLocal设定的值被持有，导致内存泄露。

上面的逻辑是清晰的，可是ThreadLocal并不会产生内存泄露，因为ThreadLocalMap在选择key的时候，
并不是直接选择ThreadLocal实例，而是ThreadLocal实例的弱引用。
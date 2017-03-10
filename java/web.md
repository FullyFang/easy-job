#Servlet
###Servlet 容器
Tomcat容器分为四个等级，真正管理servlet的容器是Context容器，一个Context对应一个web
工程(除了将Servlet包装成StandardWrapper并作为子容器添加到Context容器外，其他所有
的web.xml属性都被解析到Context中)

添加一个web应用将会创建一个StandardContext容器，并给这个容器配置必要的参数:
url(访问路径)和path(物理路径)。

最重要的一个配置是ContextConfig，负责整个web应用配置的解析工作。
最后将Context容器加到父容器Host中。

Tomcat的启动逻辑是基于观察者模式设计的，所有的容器都会继承LifeCycle接口，管理容器的
整个生命周期，所有的容器的修改和状态的改变都会由它去通知已经注册的观察者(listener,
就是说整个容器的子容器中，任何一个容器发生变化，listener都会被通知，并作出反应)。
###生命周期
web容器加载servlet，生命周期开始。通过调用servlet的init()方法进行servlet初始化。
通过调用service()方法实现，根据请求不同调用不同的do***()方法。结束服务，web容器调用servlet的
destroy()方法。
#JSP
JSP是Servlet技术的扩展，本质上是Servlet的建议方式，更强调应用的外表表达。
JSP编译后是“类servlet”。

Servlet 和 JSP 最主要的不同点在于，Servlet 的应用逻辑是在 Java 文件
中，并且完全从表示层中的 HTML 里分离开来。而 JSP 的情况是 Java 和 HTML 可以组合成
一个扩展名为.jsp 的文件。JSP 侧重于视图，Servlet 主要用于控制逻辑。
###Cookie
当一个用户通过Http访问一个服务器时，这个服务器会将一些Key/Value键值对返回给客户的浏览器
并给这些数据加上一些限制条件，在条件符合时这个用户下次访问这个服务器时，数据又被完整
地带回给服务器。
###Session
如果传回的cookie很多，无形增加客户端和服务端的数据传输量，session出现时为了解决这个问题。

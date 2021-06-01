Servlet 

概念：运行在服务器端的小程序

​	servlet就是一个接口，定义了java类被浏览器访问到（tomcat识别）的规则。

​	将来我们定义一个类，实现servlet接口，复写方法。



* 快速入门：

​	1 创建javaee项目

​	2 实现servlet接口

​	3 实现servlet接口中的抽象方法

​	4 配置servlet

```xml
<servlet>
    <servlet-name>demo1</servlet-name>
    <servlet-class>com.cufe.tomcat.servlet.Servlet01</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>demo1</servlet-name>
    <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```

* 执行原理

   1 当服务器接收到客户端浏览器的请求时，会解析请求URL路径，获取访问的Servlet的资源路径

  2 查找web.xml文件，是否有对应的<url-pattern>标签体内容

  3 如果有，则再找到对应的<servlet-class>全类名

  4 tomcat会将字节码文件加载进内存，并且创建其对象

  5 调用其方法

* servlet方法

  init方法

  service方法

  destroy方法

* servlet生命周期

  1. 被创建，执行init方法，只执行一次

     * servlet什么时候被创建？

     ​	默认情况下，第一次被访问时，Servlet创建

     ​	可以配置Servlet的创建时机

       		在第一次被访问时创建

     ​			<load-on-startup>的值为负数

      		在服务器启动时创建

     ```xml
     			<load-on-startup>的值为正数
     ```

     * servlet的init方法，只执行一次，说明一个servlet方法在内存中只存在一个对象，servlet是单例的

       ​	多个用户同时访问时，存在线程安全问题

       ​	解决：尽量不要在servlet中定义成员变量。即使定义了成员变量，也不要对其赋值

  2. 提供服务，提供service方法，可以重复执行

     每次访问servlet时，Servlet方法都会被执行一次

  3. 被销毁，提供destroy方法，只执行一次

     servlet被销毁时执行，服务器关闭时，servlet被销毁

     只有服务器正常关闭时，才会执行destroy方法

* Servlet3.0：

  * 好处：
    * 支持注解配置。可以不需要web.xml

  * 步骤：
    * 创建javaee项目，选择servlet3.0版本以上，可以不创建web.xml
    * 定义一个类，实现servlet接口
    * 复写方法
    * 在类上使用@webServlet注解，进行配置（javaee6版本以上支持web3.0） @WebServlet（“资源路径”）

#### IDEA和tomcat 的相关配置

* IDEA会为每个项目单独配置一个配置文件
  * 查看控制台的log：using CATALINA_BASE：“/Users/mx/Library/Caches/JetBrains/IntelliJIdea2021.1/tomcat/ae9d24f4-f55f-4e2c-b75e-1afa685ad86b”
  * 工作空间项目 和 tomcat部署的web项目
    * tomcat真正访问的是tomcat部署的web项目，tomcat部署的web项目对应着工作空间项目的web目录下的所有资源
    * WEB-INF目录下的资源不能被浏览器访问
    * 断点调试：使用小虫子

## servlet&http&request

* servlet体系结构

  Servlet

  GenericServlet --抽象类

  HttpServlet --抽象类

  * GneericServlet : 将Servlet接口中的其他方法做了默认空实现，只将service（）方法作为抽象
    * 将来定义Servlet类时，可以继承GenericServlet，实现service（）方法即可。

  * HttpServlet:对http协议的一种封装，简化操作
    * 定义类继承HttpServlet
    * 复写doGet/doPost方法

* servlet相关配置

  * urlparttern：servlet访问路径
  * 路径定义规则
    * /***
    * /\*\*\*/\*\*\*
    * *.do

* HTTP

  * 请求消息数据格式

    * 请求行

      * 请求方式 请求url 请求协议/版本

        GET /login.html HTTP/1.1

      * HTTP协议有7种请求方式，常用的有2种

        * GET

          请求参数在请求行中，在url后

          请求的url长度有限制

          不太安全

        * POST

          请求参数在请求体中

          请求的url长度没有限制

          相对安全

    * 请求头

      请求头名称：请求头值

      ​	常见的请求头

      ​		user_agent：浏览器告诉服务器，我访问你使用的浏览器版本信息，可以在服务器端解决浏览器兼容问题

      ​		referer：http://localhost/login.html

      ​			告诉浏览器我从哪里来？

      ​				作用：防盗链 统计工作

    * 请求空行

    * 请求体

* request和response

  * request和response对象的原理

    * request对象和response对象是由服务器创建的。我们来使用他们
    * request对象时来获取请求消息，response用来设置相应消息

  * request对象继承体系结构

    * ServletRequest     --接口
    * HttpServletRequest --接口
    * org.apache.catalina.connector.RequestFacade 类（tomcat）

  * request功能

    * 获取请求消息数据

      * 获取请求行数据

      * GET URL HTTP/1.1

        * 方法

        * 获取请求方式 GET String getMethod（） 

        * （*）获取请求目录 String getContextPath（）

        * 获取Servlet路径 String  getServletPath（）

        * 获取get方式请求参数：name=zhangsan String getQueryString 

        * （*）获取请求uri String getRequestURI（）

          StringBuffer getRequestURL（）

          Url：统一资源定位符

          url：统一资源标识符

        * 获取协议 String getProtocol（）

        * 获取客户机的IP地址 String getRemoteAddr（）

      * 获取请求头数据

        * 方法
          * （*）String getHeader（String name）通过请求头的名称获取请求头的值
          * Enumeration<String>getHeaderNames（）获取所有的请求头名称

      * 获取 请求体数据

        * 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
        * 步骤
          * 获取流对象
            * （*）BufferedReader get Reader（）获取字符输入流，只能操作字符数据
            * ServletInputStream getInputStream（）获取字节输入流，可以操作所有数据类型

      * 其他功能

        * 获取请求参数通用方式
          * String getParameter（String name）：根据参数名称获取参数值
          * String[] getParameterValues（String name）根据参数名获取参数值的数组
          * Enumeration\<string> getParameterNames():获取所有请求的参数名称
          * Map<string,string[]> getParametermap():获取所有参数的map集合
          * 中文乱码问题
            * get方式：tomcat8 已经将get方法乱码问题解决了
            * post方式：会乱码
              * 在获取参数前，设置request的编码request.setCharacterEncoding（“utf-8”）；
        * 请求转发
          * 步骤
            * 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher（String path）
            * 使用RequestDispatcher对象来进行转发：forward（ServletRequest request，ServeletResponse response）
          * 特点
            * 浏览器地址栏路径不发生变化
            * 只能访问当前服务器内部资源中
            * 转发是一次请求
        * 共享数据
          * 域对象：一个有作用范围的对象，可以在范围内共享数据
          * request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
          * 方法
            * void setAttribute（String，Object obj）：存储数据
            * Object getAttitude（String name）：通过键获取值
            * void removeAttribute（String name）通过键移除键值对
        * 获取ServletContext
          * servletContext getServletContext()

* 案例：用户登录
  * 用户登录案例需求
    * 编写login.html登录页面
      * username & password两个输入框
    * 使用Druid数据库连接池技术，操作mysql，day14数据库中的user表
    * 使用JdbcTemplate技术封装JDBC
    * 登录成功跳转到SuccessServlet展示：登录成功！用户名，欢迎您
    * 登录失败跳转到FailServlet展示：登录失败，用户名或密码错误



### 案例：用户登录

* 用户登录案例需求
  * 编写login.html登录页面
  * 使用Druid数据库连接池技术，操作mysql，day14数据库中的user表
  * 使用JdbcTemplate技术封装JDBC
  * 登录成功跳转到SuccessServlet展示：登录成功！用户名，欢迎您
  * 登录失败跳转到FailServlet展示：登录失败，用户名或密码错误

* BeanUtils工具类,简化数据封装
  * 用于封装javaBean的
  * JavaBean：标准的Java类
    * 类必须被public修饰
    * 必须提供空参的构造器
    * 成员变量必须由private修饰
    * 提供公共的setter getter
  * 功能：封装数据
  * 概念
    * 成员变量
    * 属性：setter和getter方法截取后的产物
  * 方法
    * setProperty
    * getProperty
    * populate

* HTTP协议
  * 请求消息：客户端发送给服务器端的数据
    * 数据格式
      * 请求行
      * 请求头
      * 请求空行
      * 请求体
  * 响应消息：服务器端发送给客户端的数据
    * 数据格式
      * 响应行
        * 组成：协议以及版本 响应状态码 状态码描述
        * 响应状态码：服务器告诉客户端浏览器本次请求和相应的状态
        * 分类
          * 1XX服务器接受消息，但没有接受完全，等待一段时间后，发送1XX状态码
          * 2XX 响应成功
          * 3XX 302重定向 304（访问缓存）
          * 4XX 客户端错误。代表404 请求路径没有对应的资源 405 请求方式没有对应的doXXX方法
          * 5XX 服务器端错误 代表：500（服务器内部出现异常） 
      * 响应头
        * 格式：头名称：值
        * 常见的响应头
          * Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式
          * Content-disposition：服务器告诉客户端以什么格式打开响应体数据
            * 值
              * 默认 in-line，在当前页面内打开
              * attachment：以附件形式打开响应体，文件下载
      * 响应空行
      * 响应体：传输的数据




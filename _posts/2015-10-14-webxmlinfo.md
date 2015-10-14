
---
layout: news_item
title: "Jekyll搭建blog-文章分类"
date: "2015-08-28 15:56:52 +0200"
categories: [jekyll,blog]
codeUrl: "Jekyll-feng-lei-weng-zhang"
author: fei361
---

1、启动一个WEB项目的时候，WEB容器会去读取它的配置文件web.xml，读取<listener>和<context-param>两个结点。 

2、紧急着，容创建一个ServletContext（servlet上下文），这个web项目的所有部分都将共享这个上下文。 

3、容器将<context-param>转换为键值对，并交给servletContext。 

4、容器创建<listener>中的类实例，创建监听器。 

二  Load-on-startup

Load-on-startup 元素在web应用启动的时候指定了servlet被加载的顺序，它的值必须是一个整数。如果它的值是一个负整数或是这个元素不存在，那么容器会在该servlet被调用的时候，加载这个servlet 。如果值是正整数或零，容器在配置的时候就加载并初始化这个servlet，容器必须保证值小的先被加载。如果值相等，容器可以自动选择先加载谁。  

在servlet的配置当中，<load-on-startup>5</load-on-startup>的含义是： 

标记容器是否在启动的时候就加载这个servlet。 

当值为0或者大于0时，表示容器在应用启动时就加载这个servlet； 

当是一个负数时或者没有指定时，则指示容器在该servlet被选择时才加载。 

正数的值越小，启动该servlet的优先级越高。 

三  加载顺序

首先可以肯定的是，加载顺序与它们在 web.xml 文件中的先后顺序无关。即不会因为 filter 写在 listener 的前面而会先加载 filter。最终得出的结论是：ServletContext -> listener -> filter -> servlet

        同时还存在着这样一种配置节：context-param，它用于向 ServletContext 提供键值对，即应用程序上下文信息。我们的 listener, filter 等在初始化时会用到这些上下文中的信息，那么 context-param 配置节是不是应该写在 listener 配置节前呢？实际上 context-param 配置节可写在任意位置，因此真正的加载顺序为：context-param -> listener -> filter -> servlet

        对于某类配置节而言，与它们出现的顺序是有关的。以 filter 为例，web.xml 中当然可以定义多个 filter，与 filter 相关的一个配置节是 filter-mapping，这里一定要注意，对于拥有相同 filter-name 的 filter 和 filter-mapping 配置节而言，filter-mapping 必须出现在 filter 之后，否则当解析到 filter-mapping 时，它所对应的 filter-name 还未定义。web 容器启动时初始化每个 filter 时，是按照 filter 配置节出现的顺序来初始化的，当请求资源匹配多个 filter-mapping 时，filter 拦截资源是按照 filter-mapping 配置节出现的顺序来依次调用 doFilter() 方法的。

        servlet 同 filter 类似，此处不再赘述。

       由此，可以看出，web.xml 的加载顺序是：ServletContext -> context-param -> listener -> filter -> servlet ，而同个类型之间的实际程序调用的时候的顺序是根据对应的 mapping 的顺序进行调用的。

 

四  web.xml文件详解 

 

我将自己知道的web.xml的元素整理了一下：

web.xml首先是肯定要包含它的schema.

<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
    version="2.4">

</web-app>

其它的元素都放在<web-app></web-app>之中。

<discription></discription> 是对站台的描述
<display-name></display-name> 定义站台的名称
<distributable/> 是指定该站台是否可分布式处理


<context-param></context-param> 用来设定web站台的环境参数，它包含两个子元素：
    <param-name></param-name> 用来指定参数的名称
    <param-value></param-value> 用来设定参数值

比如：
<context-param>
    <param-name>my_param</param-name>
    <param-value>hello</param-value>
</context-param>

在此设定的参数，可以在servlet中用 getServletContext().getInitParameter("my_param") 来取得


<filter></filter> 是用来声明filter的相关设定，它包含以下子元素：
    <filter-name></filter-name> 这当然就是指定filter的名字
    <filter-class></filter-class> 这是用来定义filter的类的名称
    <init-param></init-param> 用来定义参数，它有两个子元素：
        <param-name></param-name> 用来指定参数的名称
        <param-value></param-value> 用来设定参数值

比如：
<filter>
        <filter-name>setCharacterEncoding</filter-name>
        <filter-class>com.myTest.setCharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>GB2312</param-value>
        </init-param>
</filter>

与<filter></filter>同时使用的是<filter-mapping></filter-mapping> 用来定义filter所对应的URL，它有两个子元素：
    <filter-name></filter-name> 指定filter的名字
    <url-pattern></url-pattern> 指定filter所对应的URL

比如：
<filter-mapping>
        <filter-name>setCharacterEncoding</filter-name>
        <url-pattern>/*</url-pattern>
</filter-mapping>


<listener></listener> 用来设定Listener接口，它的主要子元素为
    <listener-class></listener-class> 定义Listener的类名称

比如：
<listener>
    <listener-class>com.myTest.ContextListener</listener-class> 
</listener>


<servlet></servlet> 用来声明一个servlet的数据，主要有以下子元素：
    <servlet-name></servlet-name> 指定servlet的名称
    <servlet-class></servlet-class> 指定servlet的类名称
    <jsp-file></jsp-file> 指定web站台中的某个JSP网页的完整路径
    <init-param></init-param> 用来定义参数，和前面的<init-param>差不多

同样，与<servlet></servlet>一起使用的是<servlet-mapping></servlet-mapping> 用来定义servlet所对应的URL，包含两个子元素：
    <servlet-name></servlet-name> 指定servlet的名称
    <url-pattern></url-pattern> 指定servlet所对应的URL

比如：
<servlet>
        <servlet-name>ShoppingServlet</servlet-name>
        <servlet-class>com.myTest.ShoppingServlet</servlet-class>
</servlet>
    
<servlet-mapping>
        <servlet-name>ShoppingServlet</servlet-name>
        <url-pattern>/shop/ShoppingServlet</url-pattern>
</servlet-mapping>


<session-config></session-config> 用来定义web站台中的session参数，包含一个子元素：
    <session-timeout></session-timeout> 用来定义这个web站台所有session的有效期限，单位为                                                              分钟


<mime-mapping></mime-mapping> 定义某一个扩展名和某一个MIME Type做对映，包含两个                                                        子元素：
    <extension></extension> 扩展名的名称
    <mime-type></mime-type> MIME格式

比如：
<mime-mapping>
    <extension>doc</extension>
    <mime-type>application/vnd.ms-word</mime-type>
</mime-mapping> 
<mime-mapping>
    <extension>xls</extension>
    <mime-type>application/vnd.ms-excel</mime-type>
</mime-mapping>


<welcome-file-list></welcom-file-list> 用来定义首页的列单，包含一个子元素：
    <welcome-file></welcome-file> 指定首页的文件名称

比如：
<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.html</welcome-file>
</welcom-file-list>


<error-page></error-page> 用来处理错误代码或异常的页面，有三个子元素：
    <error-code></error-code> 指定错误代码
    <exception-type></exception-type> 指定一个JAVA异常类型
    <location></location> 指定在web站台内的相关资源路径

比如：
<error-page>
    <error-code>404</error-code>
    <location>/error404.jsp</location>
</error-page>
<error-page>
    <exception-type>java.lang.Exception</exception-type>
    <location>/exception.jsp</location>
</error-page>


<taglib></taglib> 用来设定JSP网页所用到的Tag Library路径，有两个子元素：
    <taglib-uri></taglib-uri> 定义TLD文件的URI，在JSP网页中用taglib指令便可取得该URI的                                              TLD文件
    <taglib-location></taglib-location> 指定TLD文件相对于web站台的存放位置

比如：
<taglib>
      <taglib-uri>myTaglib</taglib-uri>
      <taglib-location>/WEB-INF/tlds/MyTaglib.tld</taglib-location>
</taglib>


<resource-ref></resource-ref> 定义利用JNDI取得站台可利用的资源，有五个子元素：
    <description></description> 资源说明
    <rec-ref-name></rec-ref-name> 资源名称
    <res-type></res-type> 资源种类
    <res-auth></res-auth> 资源经由Application或Container来许可
    <res-sharing-scope></res-sharing-scope> 资源是否可以共享，有Shareable和Unshareable两个                                                                     值，默认为Shareable

比如，配置数据库连接池就可在此配置：
<resource-ref>
        <description>JNDI JDBC DataSource of shop</description>
        <res-ref-name>jdbc/sample_db</res-ref-name>
        <res-type>javax.sql.DataSource</res-type>
        <res-auth>Container</res-auth>
</resource-ref>


<jsp-config> 包括<taglib> 和<jsp-property-group> 两个子元素。
    
    其中<taglib>元素在JSP 1.2时就已经存在；而<jsp-property-group>是JSP 2.0 新增的元素。
    <jsp-property-group>元素主要有八个子元素，它们分别为：
    
    1.<description>：设定的说明；
    2.<display-name>：设定名称；
    3.<url-pattern>：设定值所影响的范围，如：/CH2 或 /*.jsp；
    4.<el-ignored>：若为true，表示不支持EL 语法；
    5.<scripting-invalid>：若为true，表示不支持<% scripting %>语法；
    6.<page-encoding>：设定JSP 网页的编码；
    7.<include-prelude>：设置JSP 网页的抬头，扩展名为.jspf；
    8.<include-coda>：设置JSP 网页的结尾，扩展名为.jspf。


一个简单的<jsp-config>元素完整配置：

<jsp-config>
    <taglib>
        <taglib-uri>Taglib</taglib-uri>
        <taglib-location>/WEB-INF/tlds/MyTaglib.tld</taglib-location>
    </taglib>
    <jsp-property-group>
        <description>Special property group for JSP Configuration JSP example.</description>
        <display-name>JSPConfiguration</display-name>
        <url-pattern>/jsp/* </url-pattern>
        <el-ignored>true</el-ignored>
        <page-encoding>GB2312</page-encoding>
        <scripting-invalid>true</scripting-invalid>
        <include-prelude>/include/prelude.jspf</include-prelude>
        <include-coda>/include/coda.jspf</include-coda>
    </jsp-property-group>
</jsp-config>  


配置web.xml来限制对某些servlet的请求

有时我们只希望通过认证的用户才能请求某些servlet的话，就可以在web.xml中来进行相应的配置，来达到此目的。

这就要用到<security-constraint></security-constraint>元素。
对于tomcat，中web.xml使用security-constraint元素需要在位于<Tomcat-installation-directory>/conf/tomcat-users.xml的XML文件中创建用户名和密码。比如下面的这个tomcat-users.xml文件：

<?xml version='1.0' encoding='utf-8'?>
<tomcat-users>
  <role rolename="tomcat"/>
  <role rolename="manager"/>
  <role rolename="admin"/>
  <user username="tomcat" password="tomcat" roles="tomcat"/>
  <user username="both" password="tomcat" roles="tomcat,manager"/>
  <user username="admin" password="admin" roles="admin"/>
</tomcat-users>

此XML片段包括一个tomcat-users根元素，它包含一个或多个role和user元素。


然后在Web应用程序的web.xml中创建security-constraint、login-config和security-role元素。

<security-constraint>
      <web-resource-collection>
          <web-resource-name>HelloServlet</web-resource-name>
          <url-pattern>/HelloServlet</url-pattern>
          <http-method>GET</http-method>
          <http-method>POST</http-method>
      </web-resource-collection>
      <auth-constraint>
          <description>This applies only to the "tomcat" security role</description>
          <role-name>admin</role-name>
      </auth-constraint>
      <user-data-constraint>
          <transport-guarantee>NONE</transport-guarantee>
      </user-data-constraint>
  </security-constraint>
  
  <login-config>
      <auth-method>BASIC</auth-method>
  </login-config>
  <security-role>
      <role-name>admin</role-name>
  </security-role>

其中security-constraint元素包含一个或多个web-resource-collection元素，它是描述Web应用程序中的哪些web资源受到指定安全限制的保护。http-method元素指定安全限制覆盖的HTTP方法。上面的例子中，当我们对/HelloServlet的GET或POST请求时将触发配置的安全机制。
auth-constraint元素用于描述允许访问Web组件的安全角色。此例中安全角色的例子有tomcat、manager、admin。而只有当作为admin角色的用户才可以访问HelloServlet。

Web应用程序通过login-config元素来认证用户，并确认该用户是否为正确的角色。
longin-config包含的transport-guarantee子元素用来指定认证方法，BASIC是一种常见的Web认证方式，浏览器给用户提示一个对话框，要求输入用户名和密码，随后Tomcat将给出的用户名和密码与tomcat-users.xml中的用户名和密码进行比较，然后使用前面的security-constraint配置来确定用户是否可访问受保护的servlet。

（除BASIC外，还可以是FORM、CLIENT-CERT、DIGEST等）

其实这种认证方法实际上有两个步骤：
1、检查提供的用户名和密码是否正确。
2、判断用户是否映射到特定的安全角色。例如，用户可能提供了正确的用户名和密码，但没有映射到特定的安全角色，也将被禁止访问特定的Web资源。

五 相应元素配置    

1、Web应用图标：指出IDE和GUI工具用来表示Web应用的大图标和小图标    
<icon>    
<small-icon>/images/app_small.gif</small-icon>    
<large-icon>/images/app_large.gif</large-icon>    
</icon>    
2、Web 应用名称：提供GUI工具可能会用来标记这个特定的Web应用的一个名称    
<display-name>Tomcat Example</display-name>    
3、Web 应用描述： 给出于此相关的说明性文本    
<disciption>Tomcat Example servlets and JSP pages.</disciption>    
4、上下文参数：声明应用范围内的初始化参数。    
<context-param>    
    <param-name>ContextParameter</para-name>    
    <param-value>test</param-value>    
    <description>It is a test parameter.</description>    
</context-param>    
在servlet里面可以通过getServletContext().getInitParameter("context/param")得到    

5、过滤器配置：将一个名字与一个实现javaxs.servlet.Filter接口的类相关联。    
<filter>    
        <filter-name>setCharacterEncoding</filter-name>    
        <filter-class>com.myTest.setCharacterEncodingFilter</filter-class>    
        <init-param>    
            <param-name>encoding</param-name>    
            <param-value>GB2312</param-value>    
        </init-param>    
</filter>    
<filter-mapping>    
        <filter-name>setCharacterEncoding</filter-name>    
        <url-pattern>/*</url-pattern>    
</filter-mapping>    
6、监听器配置    
<listener>    
      <listerner-class>listener.SessionListener</listener-class>    
</listener>    
7、Servlet配置    
   基本配置    
   <servlet>    
      <servlet-name>snoop</servlet-name>    
      <servlet-class>SnoopServlet</servlet-class>    
   </servlet>    
   <servlet-mapping>    
      <servlet-name>snoop</servlet-name>    
      <url-pattern>/snoop</url-pattern>    
   </servlet-mapping>    
   高级配置    
   <servlet>    
      <servlet-name>snoop</servlet-name>    
      <servlet-class>SnoopServlet</servlet-class>    
      <init-param>    
         <param-name>foo</param-name>    
         <param-value>bar</param-value>    
      </init-param>    
      <run-as>    
         <description>Security role for anonymous access</description>    
         <role-name>tomcat</role-name>    
      </run-as>    
   </servlet>    
   <servlet-mapping>    
      <servlet-name>snoop</servlet-name>    
      <url-pattern>/snoop</url-pattern>    
   </servlet-mapping>    
   元素说明    
     <servlet></servlet> 用来声明一个servlet的数据，主要有以下子元素：    
     <servlet-name></servlet-name> 指定servlet的名称    
     <servlet-class></servlet-class> 指定servlet的类名称    
     <jsp-file></jsp-file> 指定web站台中的某个JSP网页的完整路径    
     <init-param></init-param> 用来定义参数，可有多个init-param。在servlet类中通过getInitParamenter(String name)方法访问初始化参数    
     <load-on-startup></load-on-startup>指定当Web应用启动时，装载Servlet的次序。    
                                 当值为正数或零时：Servlet容器先加载数值小的servlet，再依次加载其他数值大的servlet.    
                                 当值为负或未定义：Servlet容器将在Web客户首次访问这个servlet时加载它    
     <servlet-mapping></servlet-mapping> 用来定义servlet所对应的URL，包含两个子元素    
       <servlet-name></servlet-name> 指定servlet的名称    
       <url-pattern></url-pattern> 指定servlet所对应的URL    
8、会话超时配置（单位为分钟）    
   <session-config>    
      <session-timeout>120</session-timeout>    
   </session-config>    
9、MIME类型配置    
   <mime-mapping>    
      <extension>htm</extension>    
      <mime-type>text/html</mime-type>    
   </mime-mapping>    
10、指定欢迎文件页配置    
   <welcome-file-list>    
      <welcome-file>index.jsp</welcome-file>    
      <welcome-file>index.html</welcome-file>    
      <welcome-file>index.htm</welcome-file>    
   </welcome-file-list>    
11、配置错误页面    
一、 通过错误码来配置error-page    
   <error-page>    
      <error-code>404</error-code>    
      <location>/NotFound.jsp</location>    
   </error-page>    
上面配置了当系统发生404错误时，跳转到错误处理页面NotFound.jsp。    
二、通过异常的类型配置error-page    
   <error-page>    
       <exception-type>java.lang.NullException</exception-type>    
       <location>/error.jsp</location>    
   </error-page>    
上面配置了当系统发生java.lang.NullException（即空指针异常）时，跳转到错误处理页面error.jsp    
12、TLD配置    
   <taglib>    
       <taglib-uri>http://jakarta.apache.org/tomcat/debug-taglib</taglib-uri>    
       <taglib-location>/WEB-INF/jsp/debug-taglib.tld</taglib-location>    
   </taglib>    
   如果MyEclipse一直在报错,应该把<taglib> 放到 <jsp-config>中    
   <jsp-config>    
      <taglib>    
          <taglib-uri>http://jakarta.apache.org/tomcat/debug-taglib</taglib-uri>    
          <taglib-location>/WEB-INF/pager-taglib.tld</taglib-location>    
      </taglib>    
   </jsp-config>    
13、资源管理对象配置    
   <resource-env-ref>    
       <resource-env-ref-name>jms/StockQueue</resource-env-ref-name>    
   </resource-env-ref>    
14、资源工厂配置    
   <resource-ref>    
       <res-ref-name>mail/Session</res-ref-name>    
       <res-type>javax.mail.Session</res-type>    
       <res-auth>Container</res-auth>    
   </resource-ref>    
   配置数据库连接池就可在此配置：    
   <resource-ref>    
       <description>JNDI JDBC DataSource of shop</description>    
       <res-ref-name>jdbc/sample_db</res-ref-name>    
       <res-type>javax.sql.DataSource</res-type>    
       <res-auth>Container</res-auth>    
   </resource-ref>    
15、安全限制配置    
   <security-constraint>    
      <display-name>Example Security Constraint</display-name>    
      <web-resource-collection>    
         <web-resource-name>Protected Area</web-resource-name>    
         <url-pattern>/jsp/security/protected/*</url-pattern>    
         <http-method>DELETE</http-method>    
         <http-method>GET</http-method>    
         <http-method>POST</http-method>    
         <http-method>PUT</http-method>    
      </web-resource-collection>    
      <auth-constraint>    
        <role-name>tomcat</role-name>    
        <role-name>role1</role-name>    
      </auth-constraint>    
   </security-constraint>    
16、登陆验证配置    
   <login-config>    
     <auth-method>FORM</auth-method>    
     <realm-name>Example-Based Authentiation Area</realm-name>    
     <form-login-config>    
        <form-login-page>/jsp/security/protected/login.jsp</form-login-page>    
        <form-error-page>/jsp/security/protected/error.jsp</form-error-page>    
     </form-login-config>    
   </login-config>    
17、安全角色：security-role元素给出安全角色的一个列表，这些角色将出现在servlet元素内的security-role-ref元素的role-name子元素中。    
    分别地声明角色可使高级IDE处理安全信息更为容易。    
<security-role>    
     <role-name>tomcat</role-name>    
</security-role>    
18、Web环境参数：env-entry元素声明Web应用的环境项    
<env-entry>    
     <env-entry-name>minExemptions</env-entry-name>    
     <env-entry-value>1</env-entry-value>    
     <env-entry-type>java.lang.Integer</env-entry-type>    
</env-entry>    
19、EJB 声明    
<ejb-ref>    
     <description>Example EJB reference</decription>    
     <ejb-ref-name>ejb/Account</ejb-ref-name>    
     <ejb-ref-type>Entity</ejb-ref-type>    
     <home>com.mycompany.mypackage.AccountHome</home>    
     <remote>com.mycompany.mypackage.Account</remote>    
</ejb-ref>    
20、本地EJB声明    
<ejb-local-ref>    
     <description>Example Loacal EJB reference</decription>    
     <ejb-ref-name>ejb/ProcessOrder</ejb-ref-name>    
     <ejb-ref-type>Session</ejb-ref-type>    
     <local-home>com.mycompany.mypackage.ProcessOrderHome</local-home>    
     <local>com.mycompany.mypackage.ProcessOrder</local>    
</ejb-local-ref>    
21、配置DWR    
<servlet>    
      <servlet-name>dwr-invoker</servlet-name>    
      <servlet-class>uk.ltd.getahead.dwr.DWRServlet</servlet-class>    
</servlet>    
<servlet-mapping>    
      <servlet-name>dwr-invoker</servlet-name>    
      <url-pattern>/dwr/*</url-pattern>    
</servlet-mapping>    
22、配置Struts    
    <display-name>Struts Blank Application</display-name>    
    <servlet>    
        <servlet-name>action</servlet-name>    
        <servlet-class>    
            org.apache.struts.action.ActionServlet    
        </servlet-class>    
        <init-param>    
            <param-name>detail</param-name>    
            <param-value>2</param-value>    
        </init-param>    
        <init-param>    
            <param-name>debug</param-name>    
            <param-value>2</param-value>    
        </init-param>    
        <init-param>    
            <param-name>config</param-name>    
            <param-value>/WEB-INF/struts-config.xml</param-value>    
        </init-param>    
        <init-param>    
            <param-name>application</param-name>    
            <param-value>ApplicationResources</param-value>    
        </init-param>    
        <load-on-startup>2</load-on-startup>    
    </servlet>    
    <servlet-mapping>    
        <servlet-name>action</servlet-name>    
        <url-pattern>*.do</url-pattern>    
    </servlet-mapping>    
    <welcome-file-list>    
        <welcome-file>index.jsp</welcome-file>    
    </welcome-file-list>    

    <!-- Struts Tag Library Descriptors -->    
    <taglib>    
        <taglib-uri>struts-bean</taglib-uri>    
        <taglib-location>/WEB-INF/tld/struts-bean.tld</taglib-location>    
    </taglib>    
    <taglib>    
        <taglib-uri>struts-html</taglib-uri>    
        <taglib-location>/WEB-INF/tld/struts-html.tld</taglib-location>    
    </taglib>    
    <taglib>    
    <taglib-uri>struts-nested</taglib-uri>    
    <taglib-location>/WEB-INF/tld/struts-nested.tld</taglib-location>    
    </taglib>    
    <taglib>    
        <taglib-uri>struts-logic</taglib-uri>    
        <taglib-location>/WEB-INF/tld/struts-logic.tld</taglib-location>    
    </taglib>    
    <taglib>    
        <taglib-uri>struts-tiles</taglib-uri>    
        <taglib-location>/WEB-INF/tld/struts-tiles.tld</taglib-location>    
    </taglib>    
23、配置Spring（基本上都是在Struts中配置的）    

   <!-- 指定spring配置文件位置 -->    
   <context-param>    
      <param-name>contextConfigLocation</param-name>    
      <param-value>    
       <!--加载多个spring配置文件 -->    
        /WEB-INF/applicationContext.xml, /WEB-INF/action-servlet.xml    
      </param-value>    
   </context-param>    

   <!-- 定义SPRING监听器，加载spring -->    

<listener>    
     <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>    
</listener>    

<listener>    
     <listener-class>    
       org.springframework.web.context.request.RequestContextListener    
     </listener-class>    
</listener> 

 

其他说明：

web.xml 文件中一般包括 servlet, spring, filter, listenr的配置。那么他们是按照一个什么顺序加载呢？

加载顺序会影响对spring bean 的调用。

    比如filter 需要用到 bean ，但是加载顺序是 先加载filter 后加载spring，则filter中初始化操作中的bean为null；

首先可以肯定 加载顺序与他们在web.xml 文件中的先后顺序无关。

web.xml 中 listener 和 serverlet 的加载顺序为 先 listener 后serverlet

最终得出结果：先 listener >> filter >> servlet >>  spring

 所以，如果过滤器中要使用到 bean，可以将spring 的加载 改成 Listener的方式

<listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>


 搞定！

 关于他们的内部执行顺序，也需要注意，如下面文章中遇到的问题

web.xml的filter执行顺序导致的乱码，切记！
2008-05-02 01:38

发现引起bug的原因是web.xml的下面几行：
    <filter-mapping>
        <filter-name>SecurityFilter</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CharacterEncoding</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CharacterEncoding</filter-name>
        <url-pattern>*.jsp</url-pattern>
    </filter-mapping>

根据servlet2.3规范filter执行是按照web.xml配置的filter-mapping先后顺序进行执行，所以上面的配置会导致遇见*.do的url请求，先进行SecurityFilter的过滤器处理，这时候没有做编码处理，已经是乱码，到下面的filter处理时已经时乱码，再做编码处理已经没有用处。

修正方式，调整filter-mapping顺序，如下：

    <filter-mapping>
        <filter-name>CharacterEncoding</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CharacterEncoding</filter-name>
        <url-pattern>*.jsp</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>SecurityFilter</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>
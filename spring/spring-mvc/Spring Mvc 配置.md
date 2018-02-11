# Spring Mvc 配置

Spring的Web MVC框架与许多其他Web MVC框架一样，以请求为驱动，围绕一个中央Servlet设计，将请求发送给控制器，并提供了其他促进Web应用程序开发的功能。然而， Spring 的`DispatcherServlet` 做得更多.它和 Spring IoC 容器整合一起，它允许你使用Spring 每个特性.

Spring Web MVC `DispatcherServlet`的请求处理工作流程如下图所示。 对设计模式熟悉的读者将会认识到，`DispatcherServlet`是“前端控制器”设计模式的表达（这是Spring Web MVC与许多其他领先的Web框架共享的模式）。

![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/mvc.png)

## 1. 注解配置

##### 1. 创建一个`AbstractAnnotationConfigDispatcherServletInitializer`的子类

> - *spring mvc 容器可以自动扫描该`WebApplicationInitializer`接口的类，然后进行容器的配置和创建*

> `WebApplicationInitializer`是由Spring MVC提供的接口，可确保您的基于代码的配置被检测并自动用于初始化任何Servlet 3容器。这个名为`AbstractAnnotationConfigDispatcherServletInitializer`的接口的抽象基类实现通过简单地指定其servlet映射和列出配置类来更容易地注册`DispatcherServlet`，甚至建议您设置Spring MVC应用程序。有关更多详细信息，请参阅[基于代码的Servlet容器初始化](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-container-config)。

配置`WebApplicationInitializer`

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        ServletRegistration.Dynamic registration = container.addServlet("example", new DispatcherServlet());
        registration.setLoadOnStartup(1);
        registration.addMapping("/example/*");
    }
}
```

\* 配置`AbstractAnnotationConfigDispatcherServletInitializer`【推荐】

```java
package com.playboxjre.spring.learn.mvc;  
  
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;  
  
/* 
 * 在Servlet3.0以上的环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类，如果发现则用它来配置 
 * Servlet容器，Spring提供了这个接口的实现名为SpringServletContainerInitializer,这个类反过来又会查找实现了 
 * WebApplicationInitializer的类并把配置任务交给它们来完成,AbstractAnnotationConfigDispatcherServletInitializer的祖先类已 
 * 对该接口进行了实现 
 */  
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {  
    /** 
     * 该方法用于配置ContextLoaderListener创建的应用上下文的bean，相当于web.xml配置中的 
     * <listener>org.springframework.web.ContextLoaderListener</listener> 差异： 
     * 注解配置需要添加的是配置类<br> 
     * 文件配置ContextLoaderListener在创建时自动查找WEB-INF下的applicationContext.xml文件，当文件不止1个时需通过设置 
     * 上下文参数(context-param)配置contextConfigLocation的值 
     *  
     * @return 带有@Configuration注解的类(这些类将会用来配置ContextLoaderListener创建的应用上下文的bean) 
     */  
    @Override  
    protected Class<?>[] getRootConfigClasses() {  
        return new Class<?>[] { RootConfig.class };  
    }  
  
    /** 
     * 该方法用于配置DispatcherServlet所需bean，配置类一般用于生成控制层的bean(因Controller中一般包含对参数的设置及数据的返回) 
     * 相当于web.xml对Spring 
     * MVC的配置…<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>…<br> 
     * 配置类如ApplicationConfig.class相当于DispatcherServlet中contetConfigLocation参数对应的配置文件 
     *  
     * @return 带有@Configuration注解的类(这些类将会用来配置DispatcherServlet应用上下文中的bean) 
     */  
    @Override  
    protected Class<?>[] getServletConfigClasses() {  
        return new Class<?>[] { ApplicationConfig.class };  
    }  
  
    /** 
     * DispatcherServlet默认映射路径 
     */  
    @Override  
    protected String[] getServletMappings() {  
        return new String[] { ("/") };  
    }  
  
} 

```

##### 2. 配置`RootConfig`和`ApplicationConfig`

`RootConfig`

```java
package com.playboxjre.spring.learn.mvc;
import org.springframework.context.annotation.Configuration;
/**
	全局配置
*/
@Configuration
public class RootConfig {
 	
}
```

`ApplicationConfig`

```java
@Configuration
@EnableWebMvc // 开启Mvc支持【必须】相当于<mvc:annotation-driver/>，启用注解驱动的Spring MVC,使@RequestParam、@RequestMapping等注解可以被识别  
public class ApplicationConfig implements WebMvcConfigurer{
  
    /**
     * 开启对静态资源的访问支持
     * @param configurer
     */
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
  ...
    //配置一些需要的功能类：如 MessageSource,ViewResource,LocalSessionFactoryBean,PropertyPlaceHodlerConfiger,DataSource...等
   // 配置一些自己的Bean，Service，Repository...
}
```

##### 3. 创建自己的`controller`和`资源：jsp,html`

##### 4. 创建`index.html`

> 在`webapp`目录下创建`index.html`,简单配置就ok了

## 2. XML配置



##### 1. 配置`web.xml`

> `web.xml`配置spring web的前端控制器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index</welcome-file>
    </welcome-file-list>
    
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/application-context.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/dispatcher-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

##### 2. 配置容器加载beans文件

**classpath:spring/application-context.xml**:配置`ContextLoaderListener`加载Applciation级别配置Bean文件

> 用来配置一些应用级别的Bean

**classpath:spring/dispatcher-servlet.xml**:配置`DispatcherServlet` Servlet范围的bean配置文件

> 配置DispathcerServlet范围级别的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
               http://www.springframework.org/schema/mvc
            http://www.springframework.org/schema/mvc/spring-mvc.xsd
            http://www.springframework.org/schema/tx
            http://www.springframework.org/schema/tx/spring-tx.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd
            http://www.springframework.org/schema/cache
            http://www.springframework.org/schema/cache/spring-cache.xsd"
       default-autowire="byName">
    <context:annotation-config/>

    <!-- 或者直接开启  <context:component-scan base-package=""-->
    <mvc:annotation-driven/>
	<!-- 默认servlet处理，用来处理一些静态资源-->
    <mvc:default-servlet-handler />
    <!--<mvc:resources mapping="*.html,resources/**" location="/resources/**,/WEB-INF/resources/**"/>-->



</beans>
```

---

## 3. Bean如何归类

##### 1. Spring Web MVC中的典型上下文层次结构

> Figure18.2.Spring 
>
> ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/mvc-context-hierarchy.png)

##### 2.只有一个根上下文。 

单个DispatcherServlet方案也可能只有一个根上下文。

![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/mvc-root-context.png)
# [【Spring学习笔记-MVC-12】Spring MVC视图解析器之ResourceBundleViewResolver](http://www.cnblogs.com/ssslinppp/p/4606729.html)

## 场景

当我们设计程序界面的时候，中国人希望界面是中文，而美国人希望界面是英文。

我们当然希望后台代码不需改变，系统能够通过配置文件配置，来自己觉得是显示中文界面还是英文界面。

这是，Spring mvc的ResourceBundleViewResolver视图解析器就派上用场了。

------

http://www.cnblogs.com/rollenholt/archive/2012/12/27/2836011.html

------

## 程序设计

------

------

## 配置文件：配置ResourceBundleViewResolver视图解析器

## 控制层

------

@RequestMapping(value = "/index.action")

​    public String index(ModelMap mmMap) {

​        Person person = new Person();

​        person.setUsername("Zhangsan");

​        person.setSalary((long)3555.111);

​        person.setBirthday(new Date());

​        

​        mmMap.addAttribute("person",person);

​        

​        return "diffi18n";

​    }

------

## views_en_US.properties

------

```
diffi18n.(class)=org.springframework.web.servlet.view.JstlViewdiffi18n.url=/jsp/USA.jsp
```

------

## views_zh_CN.properties

------

```
diffi18n.(class)=org.springframework.web.servlet.view.JstlViewdiffi18n.url=/jsp/China.jsp
```

------

​    

## China.jsp

------

```
<%@ page language="java" pageEncoding="UTF-8"%><%	String path = request.getContextPath();	String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";	response.setHeader("Pragma", "no-cache");	response.setHeader("Cache-Control", "no-cache");	response.setDateHeader("Expires", 0);%><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"><html><head><title>类型转换测试</title><script type="text/javascript">var basePath = "<%=basePath%>";</script><script type="text/javascript" src="<%=basePath%>js/JQuery/jquery.min.js"></script></head><body>    <div style="padding:5px 0;">    【用户名】：${ person.username},【薪水】：${person.salary},【生日】：${person.birthday }	</div></body></html>
```

------

修改客户端语言：

![img](https://images0.cnblogs.com/blog/731047/201506/290928597901885.png)![img](https://images0.cnblogs.com/blog/731047/201506/290929003681541.png)
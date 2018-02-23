# SpringMVC中的统一异常处理

　　我们知道，系统中异常包括：编译时异常和运行时异常RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试通过手段减少运行时异常的发生。在开发中，不管是dao层、service层还是controller层，都有可能抛出异常，在springmvc中，能将所有类型的异常处理从各处理过程解耦出来，既保证了相关处理过程的功能较单一，也实现了异常信息的统一处理和维护。这篇博文主要总结一下SpringMVC中如何统一处理异常。

## **1. 异常处理思路**

　　首先来看一下在springmvc中，异常处理的思路（我已尽力画好看点了，不要喷我~）： 
![springmvc异常处理](http://img.blog.csdn.net/20160622093557944) 
　　如上图所示，系统的dao、service、controller出现异常都通过throws Exception向上抛出，最后由springmvc前端控制器交由异常处理器进行异常处理。springmvc提供全局异常处理器（一个系统只有一个异常处理器）进行统一异常处理。明白了springmvc中的异常处理机制，下面就开始分析springmvc中的异常处理。

## **2. springmvc中自带的简单异常处理器**

　　springmvc中自带了一个异常处理器叫SimpleMappingExceptionResolver，该处理器实现了HandlerExceptionResolver 接口，全局异常处理器都需要实现该接口。我们要使用这个自带的异常处理器，首先得在springmvc.xml文件中配置该处理器：

```
<!-- springmvc提供的简单异常处理器 -->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
     <!-- 定义默认的异常处理页面 -->
    <property name="defaultErrorView" value="/WEB-INF/jsp/error.jsp"/>
    <!-- 定义异常处理页面用来获取异常信息的变量名，也可不定义，默认名为exception --> 
    <property name="exceptionAttribute" value="ex"/>
    <!-- 定义需要特殊处理的异常，这是重要点 --> 
    <property name="exceptionMappings">
        <props>
            <prop key="ssm.exception.CustomException">/WEB-INF/jsp/custom_error.jsp</prop>
        </props>
        <!-- 还可以定义其他的自定义异常 -->
    </property>
</bean>1234567891011121314
```

　　从上面的配置来看，最重要的是要配置特殊处理的异常，这些异常一般都是我们自定义的，根据实际情况来自定义的异常，然后也会跳转到不同的错误显示页面显示不同的错误信息。这里就用一个自定义异常CustomException来说明问题，定义如下：

```
//定义一个简单的异常类
public class CustomException extends Exception {

    //异常信息
    public String message;

    public CustomException(String message) {
        super(message);
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

}1234567891011121314151617181920
```

　　接下来就是写测试程序了，还是使用查询的例子，如下： 
![异常](http://img.blog.csdn.net/20160622095837946) 
　　然后我们在前台输入url来测试：<http://localhost:8080/SpringMVC_Study/editItems.action?id=11>，故意传一个id为11，我的数据库中没有id为11的项，所以肯定查不到，反正让它查不到即可。这样它就会抛出自定义的异常，然后被上面配置的全局异常处理器捕获并执行，跳转到我们指定的页面，然后显示一下该商品不存在即可。所以这个流程是很清晰的。 
　　从上面的过程可知，使用SimpleMappingExceptionResolver进行异常处理，具有集成简单、有良好的扩展性（可以任意增加自定义的异常和异常显示页面）、对已有代码没有入侵性等优点，但该方法仅能获取到异常信息，若在出现异常时，对需要获取除异常以外的数据的情况不适用。

## **3. 自定义全局异常处理器**

　　全局异常处理器处理思路：

> 1. 解析出异常类型
> 2. 如果该异常类型是系统自定义的异常，直接取出异常信息，在错误页面展示
> 3. 如果该异常类型不是系统自定义的异常，构造一个自定义的异常类型（信息为“未知错误”）

　　springmvc提供一个HandlerExceptionResolver接口，自定义全局异常处理器必须要实现这个接口，如下：

```
public class CustomExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest request,
            HttpServletResponse response, Object handler, Exception ex) {

        ex.printStackTrace();
        CustomException customException = null;

        //如果抛出的是系统自定义的异常则直接转换
        if(ex instanceof CustomException) {
            customException = (CustomException) ex;
        } else {
            //如果抛出的不是系统自定义的异常则重新构造一个未知错误异常
            //这里我就也有CustomException省事了，实际中应该要再定义一个新的异常
            customException = new CustomException("系统未知错误");
        }

        //向前台返回错误信息
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("message", customException.getMessage());
        modelAndView.setViewName("/WEB-INF/jsp/error.jsp");

        return modelAndView;
    }
}1234567891011121314151617181920212223242526
```

　　全局异常处理器中的逻辑很清楚，我就不再多说了，然后就是在springmvc.xml中配置这个自定义的异常处理器：

```
<!-- 自定义的全局异常处理器 
只要实现HandlerExceptionResolver接口就是全局异常处理器-->
<bean class="ssm.exception.CustomExceptionResolver"></bean> 123
```

　　然后就可以使用上面那个测试用例再次测试了。可以看出在自定义的异常处理器中能获取导致出现异常的对象，有利于提供更详细的异常处理信息。一般用这种自定义的全局异常处理器比较多。

## **4. @ExceptionHandler注解实现异常处理**

　　还有一种是使用注解的方法，我大概说一下思路，因为这种方法对代码的入侵性比较大，我不太喜欢用这种方法。 
　　首先写个BaseController类，并在类中使用@ExceptionHandler注解声明异常处理的方法，如：

```
public class BaseController { 
    @ExceptionHandler  
    public String exp(HttpServletRequest request, Exception ex) { 
    //异常处理
    //......
    }
}1234567
```

　　然后将所有需要异常处理的Controller都继承这个BaseController，虽然从执行来看，不需要配置什么东西，但是代码有侵入性，需要异常处理的Controller都要继承它才行。 
　　关于springmvc的异常处理，就总结这么多吧。 

　　相关阅读：<http://blog.csdn.net/column/details/spring-mvc.html> 
　　学习笔记源码下载地址：<https://github.com/eson15/SpringMVC_Study>
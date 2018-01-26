# Spring相关文档翻译-chapter9(AOP部分)

翻译 2015年01月12日 19:58:09

- 标签：
- [Spring AOP](http://so.csdn.net/so/search/s.do?q=Spring%20AOP&t=blog)


- **1091

（本文的翻译基于Spring 4帮助文档。9.6以后的部分未翻译，因为感觉那些内容会很少用到，即便用到那点资料也不够。这里只是保留其提纲。）

# **9.1 简介**

面向切面编程（AOP）为面向方面编程（OOP）提供了另一种角度来实现代码结构。OOP模块的基本单元是class文件，而AOP模块的基本单元是切面。切面使得类似于事务管理的概念模块化，这些模块可以切入多个类和对象中。

Spring一个主要的框架是AOP框架。但Spring IoC容器并不依赖AOP，如果你的Spring中不需要AOP，那就可以把AOP抛开不用。

## **9.1.1AOP概念**

学习AOP，首先理解下面这些名词的真正含义。

1.1)Aspect：切面，就是能在多个类中通用的扩展功能的合集。为什么叫切面，就好像aop是组件化编程，Aspect是一个扩展功能切面，我们将扩展功能像一个可插拔的硬件一样，插入指定点，增强指定点功能，而且还能代码重用。切面包含扩展功能合集，找点，切入，工作。

1.2）join point：连接点，代码执行的一个点，如执行一个方法或处理一个异常。在spring aop中，join point总是代表一个方法的执行。

1.3）Advice：通知，切面在指定连接点执行的特定扩展功能。通知就是扩展功能，一个切面可以包含多个扩展功能，也就是一个切面可以包含多个通知。通知的类型有：around、before、after、（为什么没有throw？）在很多aop的框架中，advice的模型都类似于拦截器，在连接点的周围可以维护和建立一个拦截器链。

1.4）PointCut：切入点，一个匹配连接点（join point）的术语。aop的连接点代表一个方法，那“切入点”的功能就是匹配“方法名称”。“切入点”维护系统中“方法名称”的匹配公式，所有匹配的“方法”（join point），调用前后，都会执行advice链。切入点匹配连接点的表达式是aop的核心功能，spring默认使用AspectJ pointcut表达式。

1.5）Introduction：引入，为一个类增加额外的方法或属性申明。sping aop运行时可为任何“被通知类”引入新的接口（以及该接口的实现）。例如，你可以让一个bean实现一个IsModified接口，来简化缓存。

1.6）target object：被代理类，被一个或多个切面织入通知的对象，也就是我们业务逻辑的定义普通bean。spring中也叫advised对象。因为spring aop基于动态代理技术，当我们在业务中调用一个target的时候，实际上我们调用的是target的动态代理。

1.7）AOP proxy：代理，AOP框架自动创建的用来实现切面规则的类。在spring中，一个AOP代理有两种类型：JDK动态代理、CGLIB代理。

1.8）Weaving：织入，关联业务实现类与切面类，并创建一个被代理类。织入可以在编译期（例如，使用AspectJ编译器）、加载器或运行时完成。Spring AOP与其他纯java AOP框架一样，织入是在运行时完成的。

然后是advice类型：

2.1）Before advice：在方法调用前执行。但是不能阻止程序处理流程，除非抛出一个异常。

2.2）After returning advice:在方法正确调用后执行。注意，如果方法执行抛出异常，此advice不会执行。

2.3）After throwing advice:在方法抛出异常后执行。

2.4）After（finally） advice：在方法执行后执行，而不论方法调用是否正确。

2.5）Around advice：在方法调用的前后执行。这是一个最有效的advice类型。此类advice除了可以在方法调用前后做事情，还可改变方法的执行结果，或自定义异常。

虽然Around advice是很通用的advice，但是spring建议在具体业务的时候使用具体的advice，这样可以减少编码，还可以降低潜在的错误风险。例如，如果你只是需要在方法调用成功后更新一下缓存，那么建议使用After returning advice，而不是Around advice。

spring AOP的缺陷：1）spring AOP只提供到方法层面的切入，如果要考虑属性的访问控制，可以使用AspectJ；2）spring aop对处理fine-grained objects (such as domain objects typically)时很不方便，如果有类似需求，可以使用AspectJ。

# **9.2@AspectJ支持**

@AspectJ支持表示的意思是我们可用类上加注解的形式来定义切面。Spring的@AspectJ支持是从AspectJ 5的相关部分引入的类似技术，其注解的名字一样，支持切面与切入点所用的类库也相同。然而Spring AOP运行时，依然很纯净，不需要依赖AspectJ编译器和Weaver。

## 9.2.1 开启@AspectJ支持

开启@AspectJ支持有两种方式：用XML配置，或用JAVA类型的配置。下面以例子说明。

### **用JAVA的方式开启@AspectJ支持**

需要两个注解：@Configuration和@EnableAspectJAutoProxy

*@Configuration*

*@EnableAspectJAutoProxy*

**public class** AppConfig {

 

}

### **用XML的方式开启@AspectJ支持**

需要在XML中使用标签：<aop:aspectJ-autoproxy/>

<aop:aspectj-autoproxy/>

 

## **9.2.2定义一个切面**

开启@AspectJ支持后，你的应用环境中任何有@Aspect注解的bean定义都可以被自动Spring监测，定义为一个切面。接下来是一个不大有用的例子（只为说明配置）。

首先，应用环境中有这样一个bean，代表这个bean的类被注解@Aspect标注：

<bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">

​    *<!-- configure properties of aspect hereas normal -->*

</bean>

NotVeryUsefulAspect类代码如下：

**package** org.xyz;

**import** org.aspectj.lang.annotation.Aspect;

 

*@Aspect*

**public class** NotVeryUsefulAspect {

 

}

切面都会有一个注解@Aspect，除此之外切面与一个普通类没什么区别，都可以定义属性和方法。

*注意：在Spring中注册切面类，你可以在XML中配置一个bean定义，或者让Spring自动扫描classpath。如果你开启了Spring的自动扫描，那么切面定义时，仅仅一个@Aspect注解是不够的，还需在加一个@Component注解。*

*Spring AOP中不能为切面织入通知。因为被@Aspect标注的类是一个切面，Spring自动将这样的类排除在自动代理之外。*

## **9.2.3定义一个切入点**

Pointcut的定义是为了匹配连接点（join point），然后控制advice的执行时机。Spring aop只支持方法级的join point，所以我们可以认为pointcut的作用是匹配spring beans中的方法名。一个pointcut的申明有两部分：一个签名，匹配名字和参数；一个pointcut表达式，匹配要切入的方法在什么地方（在系统代码的哪些包中）。在aop的@AspectJ注解型配置中，pointcut签名根据方法名来定义，pointcut表达式需要@Pointcut注解标注，并且标注为@PointCut的方法名，返回值类型必须是void。

下面的简单例子可以有助于我们区别pointcut签名和pointcut表达式。例子中，定义了一个pointcut，命名为’anyOldTransfer’，这个pointcut会匹配任何名称为’transfer’的方法（无论方法参数和返回值类型）。

*@Pointcut("execution(\* transfer(..))")//* the pointcut expression

**private void** anyOldTransfer() {}*// the pointcut signature*

@Pointcut注解的表达式语法参照AspectJ 5的pointcut表达式，可以参考AspectJ的相关文档。

### **Spring aop支持的pointcut指示符（PCD）**

**Other pointcut types（spring aop不支持英文中描述的指示符）**

Thefull AspectJ pointcut language supports additional pointcut designators thatare not supported in Spring. These are:`call, get, set, preinitialization, staticinitialization,initialization, handler, adviceexecution, withincode, cflow, cflowbelow, if, @this`, and `@withincode`. Use of these pointcutdesignators in pointcut expressions interpreted by Spring AOP will result in an `IllegalArgumentException` being thrown.

Theset of pointcut designators supported by Spring AOP may be extended in futurereleases to support more of the AspectJ pointcut designators.

 

l  Execution：匹配执行方法（连接点），主要的PCD。

l  Within：匹配某个路径下定义所有类的所有方法。

l  This：匹配代理类的所有方法。

l  Target：匹配被代理类的所有方法。

l  Args：匹配某些方法，这些方法参数必须是给定类型的实例。

l  @target：匹配标记有给定注解类型的被代理类。

l  @args：匹配某些方法，这些方法运行时，传入参数类型被给定的注解类型标注。

l  @within：匹配某些方法，方法位于某些类中，类被给定的注解类型标注。

l  @annotation：匹配某些方法，这些方法被给定的注解类型标注。

 

由于spring aop只支持方法执行作为连接点，所以上面描述的一些少见的、不常用的pointcut定义可以参考AspectJ编程指南。此外，AspectJ支持基于类型的语法，在一个连接点指示符this和target代表的是同一个对象——负责执行方法的对象（被代理类）；而spring aop则不同，Spring AOP是基于代理的设计，指示符this代表的是代理类，而target代表的是被代理类。

*注意：由于spring的aop框架是基于代理的特性，无论生成JDK代理还是生成CGLIB嗲了，protected方法都不会被拦截。也就是说，任何pointcut表达式只会匹配public方法。*

*如果需求中需要拦截protected/private 方法，或者是构造方法，可以考虑spring驱动的nativeAspectJ weaving来代替spring的基于代理的aop框架。但是使用nativeAspectJ weaving前，必须保证自己对织入很熟悉，因为这会导致aop模型基于不同的特性。*

Spring AOP还支持通过bean定义的PCD（pointcut指示符），PCD可以限制匹配指定的连接点spring bean名称，也可以使用通配符（*）匹配一系列的spring bean。Bean PCD使用方式如下：

**[java]** [view plain](http://blog.csdn.net/lh87522/article/details/42647085#) [copy](http://blog.csdn.net/lh87522/article/details/42647085#)

1. bean(idOrNameOfBean)  
2. //使用方式：@Pointcut(“bean(prefixBeanName*)”)  

idOrNameOfBean代表任意spring bean的名称：支持有限制的通配符（*），如prefixBeanName*，可以匹配名称前缀为prefixBeanName的bean。所以如果你建立了一系列命名规范的spring bean，通过有限制的通配符表达式，可以很容易的完成筛选。如果要与其他Pointcut指示符连接使用，bean PCD还可以用&&、||、!(非)符号。

*注意：bean PCD是Spring AOP的特有性质，native AspectJ weaving没有此性质。它是Spring AOP对AspectJ PCDs定义的扩展。*

*Bean PCD管理是在instance level（基于Spring bean的概念），而不是限制在type level。（而基于织入的AOP就有此限制。）基于实例的pointcut指示符（Instance-basedpointcut designators）是Spring基于代理AOP框架的特有功能，此功能让Sping AOP与Spring bean factory紧密结合，通过指定的bean名称就能自然、直截了当的识别。*

### **结合使用pointcut表达式**

切入点表达式可与&&、||、!符号结合使用。同时，切入点表达式之间可以通过切入点表达式的名称（也就是上面描述过的签名）关联。下面的是三个切入点表达式的例子：a`nyPublicOperation` （表示匹配所有定义为public的方法）；inTrading（表示匹配位于trading模块的所有方法）   `tradingOperation` （表示匹配所有定义为public的方法和位于trading模块下的方法，这里切入点表达式就是通过关联其他切入点名称（签名）来定义的）

@Pointcut("execution(public* **(..))")**

**    private voidanyPublicOperation() {}**

** **

**   @Pointcut("within(com.xyz.someapp.trading..**)")

​    **private void** inTrading() {}

 

​    *@Pointcut("anyPublicOperation()&& inTrading()")*

​    **private void** tradingOperation() {}

上面的例子是一个最佳实践：如果要定义一个较复杂的切入点表达式，先将表达式拆分成更小的命名组件，最后在组合成复杂表达式。如果使用其他切入点的名称来定义一个更复杂的表达式，那么被引用切入点方法的访问权限与java方法访问权限保持一致。（public：任何地方都可引用，protected：子类中可以引用，private：只有自己可以引用）如上面的两个定义：`anyPublicOperation` 和`inTrading`，在其他切面中就不能引用它们了。访问权限不影响切入点的匹配。

### **共享通用切入点定义**

在企业应用开发中，你可能想只用几个切面就搞定应用的各个模块和一系列特殊的业务操作的切入。我们推荐定义一个“SystemArchitecture”切面，它可以捕获通用的切入点表达式。这种典型的切面如下：

**[java]** [view plain](http://blog.csdn.net/lh87522/article/details/42647085#) [copy](http://blog.csdn.net/lh87522/article/details/42647085#)

1. package com.business.aop;  
2.   
3. import org.aspectj.lang.annotation.Aspect;  
4. import org.aspectj.lang.annotation.Pointcut;  
5.   
6. @Aspect  
7. public class SystemArchitecture {  
8. ​    /** 
9. ​     * 匹配com.xyz.someapp.web模块下定义的所有类的所有方法 
10. ​     * 包括子目录 
11. ​     * 顾名思义，定义web层切入点 
12. ​     */  
13. ​    @Pointcut("within(com.xyz.someapp.web..)")  
14. ​    public void inWebLayer() {  
15. ​    }  
16. ​    /** 
17. ​     * 匹配com.xyz.someapp.service模块下定义的所有类的所有方法 
18. ​     * 顾名思义，定义service层的切入点 
19. ​     */  
20. ​    @Pointcut("within(com.xyz.someapp.service..)")  
21. ​    public void inServiceLayer() {  
22. ​    }  
23. ​    /** 
24. ​     * 匹配com.xyz.someapp.dao模块下定义的所有类的所有方法 
25. ​     * 顾名思义，定义dao层的切入点 
26. ​     */  
27. ​    @Pointcut("within(com.xyz.someapp.dao..)")  
28. ​    public void inDataAccessLayer() {  
29. ​    }  
30. ​    /** 
31. ​     * 匹配com.xyz.someapp模块下，当前路径或子目录中，“service包”中接口中定义的方法。 
32. ​     * 前提是，接口的实现类在“service包”的子包中。 
33. ​     *  
34. ​     * 注意：如果我们的服务层是按供区域归类的，（如：两个“service包”的路径分别是 
35. ​     * com.xyz.someapp.abc.service和com.xyz.someapp.def.service）， 
36. ​     * 那么切入点的表达式就要改为：execution(* com.xyz.someapp..service..(..)) 
37. ​     *  
38. ​     * 此外，我们还可以用bean PCD（pointcut指示符）来定义表达式。如： 
39. ​     * @Pointcut("bean(Service)")。但前提是我们为service层定义spring bean时， 
40. ​     * bean的命名风格要保持一致。 
41. ​     */  
42. ​    @Pointcut("execution( com.xyz.someapp..service..(..))")  
43. ​    public void businessService() {  
44. ​    }  
45. ​    /** 
46. ​     * 匹配com.xyz.someapp.dao中接口中定义的方法。 
47. ​     * 前提是，接口定义在“dao包”中，而接口实现类在“dao包”的子包中 
48. ​     */  
49. ​    @Pointcut("execution( com.xyz.someapp.dao..(..))")  
50. ​    public void dataAccessOperation() {  
51. ​    }  
52. }  

上述代码切面中的切入点定义为public，你可以在系统中的地方访问这些切入点。如，要配置service层可的事务性，我们可以如此定义：

```
<aop:config>
```

```
    <aop:advisor
```

```
        pointcut="com.xyz.someapp.SystemArchitecture.businessService()"
```

```
        advice-ref="tx-advice"/>
```

```
</aop:config>
```

```
 
```

```
<tx:advice id="tx-advice">
```

```
    <tx:attributes>
```

```
        <tx:method name="*" propagation="REQUIRED"/>
```

```
    </tx:attributes>
```

```
</tx:advice>
```

上面的标签：<aop:config>、<aop:advisor>将在9.3中讨论。  而<tx:advice>在12章中讨论。

### **几个栗子**

Spring AOP的用户偏向于与使用Pointcut PCD：execution。Execution 表达式的规范如下：

 

```
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern)
```

```
            throws-pattern?)
```

```
//后缀?的项，表示可选项；无?的项，是必填项
```

**modifiers-pattern**：匹配修饰语，即public/protected/private。可选。

**Ret-type-pattern：**匹配返回值类型。

**Declaring-type-pattern：**匹配类路径。要切入方法所在类，所在的路径。可选。

**Name-pattern：**匹配方法名。

**Param-patturn：**匹配方法参数类型。

**Throws-pattern：**匹配方法抛出的异常。可选。

Ret-type-pattern可以指定方法返回参数是特定的类型，通常我们都会配置为通配符（*），表示匹配所以返回值类型，但如果要配置返回值类型，最好指定类的全路径限定名；name-pattern匹配方法名，可以是通配符（*）或部分名称加通配符（partName*、set*等）；param-patturn稍微有点复杂，它用来匹配方法参数类型：

()：表示匹配无参方法；

(..)：表示匹配人员参数类型、任意参数数量的方法；

(*)：匹配只有一个参数的方法，参数类型任意；

(*,String)：表示匹配有两个参数的方法，参数1可以是任意类型，参数2是String类型。

下面是一些常用pointcut表达式的例子：

l 匹配所以public方法：

```
execution(public * *(..))
```

l 匹配所有以set开头的方法：

```
execution(* set*(..))
```

l 匹配com.xyz.service.AccountService类中的所有方法：

```
execution(* com.xyz.service.AccountService.*(..))
```

l 匹配com.xyz.service包中，当前路径包含所有类的所有方法：

```
execution(* com.xyz.service..(..))
```

l 匹配com.xyz.service包中，当前路径或子目录中，所有类的所有方法：

```
execution(* com.xyz.service...(..))
```

l 匹配com.xyz.service包中，当前路径所有类的所有方法：

```
within(com.xyz.service.*)	//Spring AOP only
```

l 匹配com.xyz.service包中，当前路径或子目录中，所有类的所有方法：

```
within(com.xyz.service..*)     //Spring AOP only
```

l 匹配所有实现com.xyz.service.AccountService接口的实现类生成的代理类：

```

```

```
this(com.xyz.service.AccountService)	//Spring AOP only
```

*注意：this PCD通常用于绑定的形式，在下面讲advice的时候，就可以看到advice如何访问proxy类（代理类）。*

l 匹配所有实现com.xyz.service.AccountService接口的实现类（被代理类）

```
target(com.xyz.service.AccountService)
```

[*注意：target PCD*]()*通常用于绑定形式，在下面讲advice的时候，可以看到advice如何访问target类（被代理类）。*

l 匹配只有一个参数的方法，并且参数类型在运行时为java.io.Serializable的实例

**[java]** [view plain](http://blog.csdn.net/lh87522/article/details/42647085#) [copy](http://blog.csdn.net/lh87522/article/details/42647085#)

1. args(java.io.Serializable)  

注意：常用于绑定形式，在下面讲advice的时候，可以看到advice如何访问被调用的方法参数。

*这里的args与*`execution(**(java.io.Serializable))`* 不同：args PCD匹配的是运行时传参为Serializable，而executionPCD匹配方法签名声明为Serializable。*

l 匹配所有标有@Transactional注解的被代理类的所有方法：

```
@target(org.springframework.transaction.annotation.Transactional)
```

[*注意：target PCD*]()*的另一种绑定形式，在下面讲advice的时候，可以看到advice如何访问注解对象。*

l 匹配所以标有@Transactional注解的被代理的申明类型的所有方法：

```
@within(org.springframework.transaction.annotation.Transactional)
```

*注意：within PCD的也可被用于绑定形式，在下面讲advice的时候，可以看到advice如何访问注解对象。*

l  匹配所有标有@Transactional注解的方法：

```
@annotation(org.springframework.transaction.annotation.Transactional)
```

l  匹配只有一个参数的方法，但在运行时传递参数类型必须要有@Classified注解：

```
@args(com.xyz.security.Classified)
```

l  匹配spring bean名称为tradeService的所有方法：

```
bean(tradeService)
```

l  匹配spring bean名称，以0个或多个字符开头，但以Service结尾的bean的所有方法：

```
bean(*Service)
```

### **写好的切入点表达式**

在编译期间，AspectJ会试图对切入点匹配的性能进行优化。因为检查代码，并确定每个连接点是否与定义的切入点匹配是一件消耗资源的活。（匹配又分为静态匹配——编译期匹配和动态匹配——运行时匹配。动态匹配用于静态匹配并不能完全确定代码在运行时是否能正常织入的情况。编译时，每遇到一个切入点定义，AspectJ都会从匹配处理的性能角度，优化重写切入点定义。这是什么意思呢？就是说，切入点用DNF（Disjunctive Normal Form）重写，然后切入点会被分类排序，哪些切入点定义容易检查会先执行。这意味着你不用关心切入点定义对众多PCD的支持性能如何。

然而，AspectJ如何工作，也取决于开发者如何定义。为了优化匹配性能，你需要考虑：切入点为了尽可能多的匹配搜索空间中定义的连接点，它的实现和限制是什么？现有的pointcut指示符被很自然的分为三组，分别是：kinded、scoping和context

Kinded指示符：匹配指定的连接点。这样的PCD有：execution、get、set、call、handler等。

Scoping指示符：匹配一组连接点。（一组连接点中，包含指定的连接点）。这样的PCD有：within、withincode等。

Contextual指示符：这种匹配基于上下文。这样的PCD有：this、target、@annotation（这些指示符是在运行情况下，根据连接点的上下文随机绑定）

一个好的切入点定义，应该包含上面所述的前两组指示符（kinded、scoping）。而使用contextual指示符则要根据开发需要：匹配是否要用到连接点的上下文、或advice中是否需要访问上下文。当然，只定义kinded指示符或只定义contextual指示符也能工作，但由于额外的分析处理，会影响织入的性能（时间和内存）。Scoping指示符匹配性能最好，因为AspectJ可以直接忽略一组无需匹配处理的连接点——所以好的切入点定义应该尽可能的包含起码一个scoping指示符。

## 9.2.4定义通知（Declaring Advice）

Advice与pointcut表达式关联，通过pointcut表达式，可以控制advice的执行时机，如在方法前执行-before、在方法后执行-after、在方法前后执行-around。Pointcut表达式可以引用另一个pointcut表达式的签名，也可以直接定义。

### **Before advice**

Before advice在切面中用@Before注解定义：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.Before;

 

*@Aspect*

**public class** BeforeExample {

 

​    *@Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")*

​    **public void** doAccessCheck() {

​        *// ...*

​    }

​    //括号里是对一个切入点签名的引用

}

 

 

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.Before;

 

*@Aspect*

**public class** BeforeExample {

 

​    @Before("execution(* com.xyz.myapp.dao.**.**(..))")

​    **public void** doAccessCheck() {

​        *// ...*

​    }

​    //括号里为Before advice定义了切入点。

}

### **After-returning advice**

After-returning advice在被代理类的方法正确执行后返回。用@AfterReturning注解定义：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.AfterReturning;

 

*@Aspect*

**public class** AfterReturningExample {

​    *@AfterReturning("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")*

​    **public void** doAccessCheck() {

​        *// ...*

​    }

 

}

如果你在advice中需要访问返回值结果，你可以修改@AfterReturning注解的定义参数，来绑定返回值。如下：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.AfterReturning;

 

*@Aspect*

**public class** AfterReturningExample {

 

​    *@AfterReturning(*

*       pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",*

*       returning="retVal")*

​    **public void** doAccessCheck(Object retVal) {

​        *// ...*

​    }

//注意两处修改：注解增加returning属性，advice增加传入参数

}

上面注解中returning的值“retVal”必须与advice方法中传入参数的名称保持一致。注意：绑定返回值的同时，返回值的类型也会被用于连接点的匹配筛选。上面的Object类型，则可以匹配所有的返回值类型。

### **After-throwing advice**

After-throwing advice在连接点抛出异常后执行。用@AfterThrowing注解定义：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.AfterThrowing;

 

*@Aspect*

**public class** AfterThrowingExample {

 

​    *@AfterThrowing("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")*

​    **public void** doRecoveryActions() {

​        *// ...*

​    }

 

}

如果需要指定抛出异常的类型，或者在After-throwing advice方法中访问异常。修改@AfterThrowing注解，添加throwing属性来绑定异常，同时也限制异常类型。如果不需要限制异常类型，则绑定异常类型可定义为Throwable（所有异常的父类）。实例如下：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.AfterThrowing;

 

*@Aspect*

**public class** AfterThrowingExample {

 

​    *@AfterThrowing(*

*       pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",*

*        throwing="ex")*

​    **public void** doRecoveryActions(DataAccessException ex) {

​        *// ...*

​    }

 

}

@AfterThrowing注解属性throwing的值“ex”必须与advice方法参数名称保持一致。同时，绑定的异常类型也将对连接点抛出的异常类型做限制。上面的例子就只匹配抛出DataAccessException异常的连接点。

### **After（finally） advice**

After（finally） advice在一个方法运行后执行，而不管方法执行正确与否。用@After注解定义。After advice必须兼容方法运行正常、异常的情况，典型的应用就是用于释放资源。实例如下：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.After;

 

*@Aspect*

**public class** AfterFinallyExample {

 

​    *@After("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")*

​    **public void** doReleaseLock() {

​        *// ...*

​    }

 

}

### **Around advice**

Around advice在连接点方法调用前后执行。因为Around advice将被代理类的调用方法暴露出来，所以我们可以自行决定在方法调用前后我们要干什么，甚至，我们可以决定是否调用此方法。Around advice通常的使用场景：需要获取方法调用前和调用后的状态，并且保证线程安全。（如：启动或停止定时器）。当然，如果上面advice能实现的功能，我们不推荐使用Around advice。（因为能减少出错的可能）

Around advice用注解@Around来定义。在advice的方法中，第一个参数必须是ProceedingJoinPoint，在方法体内部可以通过ProceedingJoinPoint.process()语句调用连接点方法。同时process()方法可以传入一个Object[]类型的参数，代表连接点的方法参数。

[*注意：AspectJ*]()*编译器编译Around advice时，带Object[]参数的方法，比无参方法要难编译。Around advice使用传统的AspectJ语言编写，proceed()方法的参数与传入Aroundadvice的参数（不是被代理类连接点方法的参数），在数量上要保持一致。这是因为在参数绑定时，传给proceed()的参数值会取代连接点的原始值。（这没道理！）Spring AOP的实现方式要更好用些。你目前只有知道编译Spring编写的@AspectJ切面，并调用带参proceed()方法，与AspectJ编译器和织入是有区别的。有一种编写切面的方法，可以100%兼容Spring AOP和AspectJ，这在下面的“advice参数”中讨论。*

Around advice示例：

**import** org.aspectj.lang.annotation.Aspect;

**import** org.aspectj.lang.annotation.Around;

**import** org.aspectj.lang.ProceedingJoinPoint;

 

*@Aspect*

**public class** AroundExample {

 

​    *@Around("com.xyz.myapp.SystemArchitecture.businessService()")*

​    **public** Object doBasicProfiling(ProceedingJoinPointpjp) **throws** Throwable {

​        *// start stopwatch*

​        Object retVal =pjp.proceed();

​        *// stop stopwatch*

​        **return** retVal;

​    }

//注意传入第一个参数为：ProceedingJoinPoint

}

Around advice的返回值是被代理类连接点调用返回的值。一个最简单的缓存切面示例：在调用方法前，先从缓存中取数据，如果有数据，那么就不调用方法，直接返回；如果缓存中没数据，在调用proceed()方法，获取返回值。注意：proceed()方法调用一次或多次，甚至可以不调用，这些写法都是合理的。

### **Advice参数**

Spring提供了很全面的advice类型，你可以在advice签名中定义参数（如前面的示例：returning、throwing），而不是总通过Object[]数组来访问参数。现在我们来讨论如何在advice方法体中访问参数和连接点信息。首先，写一个一般的advice，并获取被切入连接点的信息。

### **访问当前连接点**

任何advice都可以将方法的第一参数定义为org.aspectj.lang.JoinPoint类型。但注意，Around advice第一个参数类型是ProceedingJoinPoint，其是JoinPoint的子类。JoinPoint提供了很多有用的方法。如：

getArgs()：获取连接点方法参数列表。

getThis()：获取代理类的引用。

getTarget()：获取被代理的引用。

getSignature()：获取连接点方法的描述。

toString()：打印连接点方法的有用信息。

JoinPoint类的其他方法，可以参考相关帮助文档。

### **Advice参数传递**

前面已经介绍了如何在after-returningadvice中绑定返回值，以及在throwing advice中绑定异常。为了能在advice方法体中访问参数，你可以用args属性来绑定参数。下图通过例子来说明参数绑定。假设你需要切入dao操作，并将Account对象作为第一个参数，然后再advice方法体中访问account。代码如下

*@Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation()&& args(account,..)")*

**public void** validateAccount(Account account) {

​    *// ...*

}

上面的 `args(account,..)`  部分由两层含义：一，匹配的连接点方法至少有一个参数，并且传入参数必须包含一个Account类的实例；二，account参数使得Account对象信息在advice方法体中可以被访问。

另一种方法是，定义一个切入点，并匹配含有Account对象的连接点。然后定义advice时，引用切入点的签名。代码如下：

```
@Pointcut("com.xyz.myapp.SystemArchitecture.dataAccessOperation() && args(account,..)")
```

```
private void accountDataAccessOperation(Account account) {}
```

```
 
```

```
@Before("accountDataAccessOperation(account)")
```

```
public void validateAccount(Account account) {
```

```
    // ...
```

```
}
```

如果要了解args的更多信息，请参考AspectJ编程指南。

代理类（this），被代理类（target），注解（@within、@target、@annotation、@args）也可以用类似的方式绑定。下面的例子展示了如何匹配带有@Auditable注解的连接点，并获取注解信息。

首先，定义一个@Auditable注解：

```
@Retention(RetentionPolicy.RUNTIME)
```

```
@Target(ElementType.METHOD)    //注意，标注有此注解
```

```
public @interface Auditable {
```

```
    AuditCode value();
```

```
}
```

然后，定义advice，并匹配标有@Auditable注解的连接点方法：

```
@Before("com.xyz.lib.Pointcuts.anyPublicMethod() && @annotation(auditable)")
```

```
public void audit(Auditable auditable) {
```

```
    AuditCode code = auditable.value();
```

```
    // ...
```

```
}
```

### **Advice参数与泛型**

Spring AOP还可以处理类申明和方法参数中包含的泛型。现在，我们假设有这样一个泛型：

**public interface** Sample<T> {

​    **void** sampleGenericMethod(T param);

​    **void** sampleGenericCollectionMethod(Collection<T>param);

}

你可以通过指定参数类型来拦截方法类型。换句话说，就是你可以在advice方法参数中定义参数类型，并绑定此类型到切入点表达式，然后连接点方法只有传入相同的的参数类型，才会被拦截。定义示例如下：

*@Before("execution(\* ..Sample+.sampleGenericMethod(*)) && args(param)")     //注意Sample类有个符号”+”*

**public void** beforeSampleMethod(MyType param) {

​    *// Advice implementation*

}

这种泛型参数拦截的好处显而易见。但需要注意的是，上面的配置方法对于泛型集合是无效的，也就是对Sample<T>类中的第二个方法无效。下面是无效的配置：

```
@Before("execution(* ..Sample+.sampleGenericCollectionMethod(*)) && args(param)")
```

```
public void beforeSampleMethod(Collection<MyType> param) {
```

```
    // Advice implementation
```

```
}
```

如果我们想让类似上面的配置生效，就要做额外的检查，检查Collection中每一个元素是否是正确的参数类型，同时还要考虑null元素的存在。这种做法是不合理的。为了实现与上面代码所需要的功能，需要修改参数类型为Collection<?>，然后手动检查集合元素类型。

### **检查参数名称**

Advice调用时绑定参数，依赖于切入点表达式中定义的参数名。此参数名申明了advice和pointcut方法签名中的参数名。Java的反射机制对此参数名无效，所以Spring AOP用下面的方法来检查参数。

l 如果开发者清楚的定义了参数的名称，那么这些参数名可以被使用：advice和pointcut注解都有一个可选的“argNames”属性，该属性可以用于指定被注解方法的参数名——这些参数名在运行时有效。例如：

```
@Before(value="com.xyz.lib.Pointcuts.anyPublicMethod() && target(bean) && @annotation(auditable)",
```

```
        argNames="bean,auditable")
```

```
public void audit(Object bean, Auditable auditable) {
```

```
    AuditCode code = auditable.value();
```

```
    // ... use code and bean
```

```
}
```

如果第一个参数advice的第一个参数类型是JoinPoint、ProceedingJoinPoint，或是JoinPoint.StaticPart，在使用argNames属性时，可以不用定义第一个参数名。示例如下：

```
@Before(value="com.xyz.lib.Pointcuts.anyPublicMethod() && target(bean) && @annotation(auditable)",
```

```
        argNames="bean,auditable")
```

```
public void audit(JoinPoint jp, Object bean, Auditable auditable) {
```

```
    AuditCode code = auditable.value();
```

```
    // ... use code, bean, and jp
```

```
}       
```

```
//与上面一个代码的区别是多了一个jp参数，但：argNames=”bean,auditable”
```

```
//而不是:argNames=”jp,bean,auditable”
```

上面的代码对第一个参数为JoinPoint、ProceedingJoinPoint、JoinPoint.StaticPart类型的参数名做了特殊处理，好处是advice不需要获取其他任何连接点的上下文信息。这种情况下，你可以不使用argNames属性，就像下面的例子一样：

```
@Before("com.xyz.lib.Pointcuts.anyPublicMethod()")
```

```
public void audit(JoinPoint jp) {
```

```
    // ... use jp
```

```
}
```

l 使用”argNames”属性显得有些多余，所以如果没有指定”argNames”属性，Spring AOP会查询class文件的调试信息，从局部变量表中确定参数名。这种信息只有在class文件用带调试信息参数（至少有一个参数：’-g:vars’）的编译后才会有。按此种方式编译的结果是：（1）代码要稍微好懂一些（反向工程）；（2）class文件size会稍微大一些（无所谓）；（3）优化局部变量，剔除不会被使用的变量。换而言之，使用带调试信息参数的编译，不会带来任何麻烦。

*注意：如果用AspectJ编译器来编译@AspectJ切面，即便没有调试信息也无所谓，AspectJ编译器会自动保留有用的信息。*

l 如果编译代码并未产生必要的调试信息，Spring AOP试图推断一对参数是否绑定。（例如，如果切入点表达式中只绑定了一个参数，且advice方法中也只有一个参数，那很容易推出这是一对可绑定的参数）。如果可用信息不足以确定一对绑定，会抛出IllegalArgumentException异常。

l 如果上面的绑定策略都失败了，那将抛出IllegalArgumentException。

### **继续讨论参数**

我们上面提到过如何写一个调用带参数的proceed()方法，并且兼容Spring AOP和AspectJ。（见Around advice部分）解决方案很简单，只要确保advice方法参数按顺序绑定方法参数即可。

```
@Around("execution(List<Account> find*(..)) && " +
```

```
        "com.xyz.myapp.SystemArchitecture.inDataAccessLayer() && " +
```

```
        "args(accountHolderNamePattern)")
```

```
public Object preProcessQueryPattern(ProceedingJoinPoint pjp,
```

```
        String accountHolderNamePattern) throws Throwable {
```

```
    String newPattern = preProcess(accountHolderNamePattern);
```

```
    return pjp.proceed(new Object[] {newPattern});
```

```
}
```

```
//args PCD，匹配只有一个参数的方法，且参数类型被指定。如:args(java.io.Serializable)
```

```
//上面的绑定，用了args指示符，但括号中的参数是一个名称，而不是一个类型，
```

```
//此名称与advice方法参数名称保持一致，完成绑定。
```

在很多情况下，你都可能会用到类似于上面的绑定方式。

### **Advice优先级**

如果一个连接点织入了多个通知会是什么情况？Spring AOP与AspectJ遵从同样的优先级规则来执行advice。分两种情况：1）调用方法前，高优先级的advice先执行（如，两个Before advice都切入同一个连接点，谁的优先级高，谁先执行）；2）调用方法后，高优先级的advice后执行（如，两个After advice切入同一个连接点，谁的优先级高，谁后执行）。

如果织入同一个连接点的advice位于两个不同的切面，除非有明确的定义，否则执行顺序是不明确的。通过指明优先级可以控制执行顺序。指明优先级的方式与spring保持一致，有两种方式：1）定义切面时，用切面实现org.springframework.core.Ordered接口；2）用@Order注解标注切面。两个切面，谁的Ordered.getValue()（或注解的value）高，谁的优先级就高。

如果织入同一个连接点的advice位于一个相同的切面，执行顺序未定义。考虑到此情况下连接点advice方法会冲突，我们只能重构advice，将相同类型的advice重构到不同的切面中——这样就可以在切面层级上定义advice的执行顺序。

## **9.2.5引入（Introductions）**

Introductions（在AspectJ中叫inter-type declarations）可以在运行时，让一个被代理类实现一个指定的接口，接口实现的定义写在切面中。如此一来，被代理类的功能就得到了扩充。

在切面中定义Introduction使用注解@DeclareParents。该注解的含义是：申明匹配成功的类将拥有一个新的父类（这也是注解名称的来由）。例如，给定一个接口UsageTracked，以及该接口的一个实现类DefaultUsageTracked，下面的切面申明了所有实现service接口的类，同样要实现UsageTracked接口。代码如下：

```
@Aspect
```

```
public class UsageTracking {
```

```
 
```

```
    @DeclareParents(value="com.xzy.myapp.service.*+", defaultImpl=DefaultUsageTracked.class)
```

```
    public static UsageTracked mixin;
```

```
 
```

```
    @Before("com.xyz.myapp.SystemArchitecture.businessService() && this(usageTracked)")
```

```
    public void recordUsage(UsageTracked usageTracked) {
```

```
        usageTracked.incrementUseCount();
```

```
    }
```

```
 
```

```
}
```

要实现的接口定义在注解的域中。@DeclareParents注解的value属性值，是一种AspectJ的类型匹配方式。注意上面代码中的Before advice，可以直接转化UsageTracked的实现类，并调用类中的方法。使用示例如下：

```
UsageTracked usageTracked = (UsageTracked) context.getBean("myService");
```

```
//这里假设”muService”是一个用service接口的实现类定义的bean名称
```

## **9.2.6切面的实例模型**

默认情况下，切面在应用容器中是单例的。AspectJ将这种默认称为单实例模型（singleton *instantiation*Model）。It is possible to define aspects with alternate lifecycle（不太确定这句话的意思，可能是表达可以将切面定义成非单例模式）：Spring支持AspectJ的perthis和pertarget实例化模型（而PCD：percflow、percfloebelow、pertypewithin是AspectJ特有的，Spring AOP还不支持）。

Perthis切面的定义是在@Aspect注解中使用perthis从句。示例如下：

*@Aspect("perthis(com.xyz.myapp.SystemArchitecture.businessService())")*

**public class** MyAspect {

 

​    **private int** someState;     //有状态的切面。如果用单例，是有问题的

 

​    *@Before(com.xyz.myapp.SystemArchitecture.businessService())*

​    **public void** recordServiceUsage() {

​        *// ...*

​    }

 

}

Perthis从句的作用：为每一个执行业务处理的单独的服务层对象创建一个切面实例。切面实例的创建发生在在服务层对象的方法首次被调用之时，服务层对象生命周期结束，切面实例的生命周期也结束。切面实例创建前，切面中所有advice不能被执行，只有创建了切面实例，里面的advice才能在匹配连接点执行，这种限制只针对一对关联的服务层对象和切面实例。想获取更多关于per-从句的信息，可以参考AspectJ编程指南。

Pertarget实例模型与perthis一模一样。唯一的区别是：perthis，为每个与切入点匹配的服务层对象（this，代理类）创建一个切面实例；pertarget，为每个与切入点匹配的target（被代理类/委托类）创建一个切面实例。

## **9.2.7几个栗子**

关于AOP功能模块已经介绍完了。现在可以将这些模块组装起来，实现一些简单的例子。

服务层执行业务逻辑时，有时可能会由于并发问题导致失败（如死锁）。很多并发问题后，可以进行一次重试，也许问题就能解决。这种重试的处理方式对服务层业务就很适用（如幂运算，你不需要因并发错误，将错误冲突返回给用户来解决）。很明显，我们更乐意重新执行一次业务逻辑，而不是给用户返回一个PessimisticLockingFailureException异常。对于这种情况，我们需要切入很多个服务层业务类来编写重试的代码，那么切面编程就是一个很好的主义。

因为我们要对失败的方法进行重试，那么可以选择Around advice，因为它可以多次调用连接点方法。下面是一个基本的切面实现：

*@Aspect*

**public class** ConcurrentOperationExecutor **implements** Ordered {

 

​    **private static final int** DEFAULT_MAX_RETRIES = 2;

 

​    **private int** maxRetries = DEFAULT_MAX_RETRIES;

​    **private int** order = 1;

 

​    **public void** setMaxRetries(**int** maxRetries) {

​        **this**.maxRetries = maxRetries;

​    }

 

​    **public int** getOrder() {

​        **return this**.order;

​    }

 

​    **public void** setOrder(**int** order) {

​        **this**.order = order;

​    }

 

​    *@Around("com.xyz.myapp.SystemArchitecture.businessService()")*

​    **public** Object doConcurrentOperation(ProceedingJoinPointpjp) **throws** Throwable {

​        **int** numAttempts = 0;

​       PessimisticLockingFailureException lockFailureException;

​        **do** {

​            numAttempts++;

​            **try** {

​                **return** pjp.proceed();

​            }

​            **catch**(PessimisticLockingFailureException ex) {

​                lockFailureException= ex;

​            }

​        } **while**(numAttempts <= **this**.maxRetries);

​        **throw** lockFailureException;

​    }

 

}

上面的例子，切面实现了Ordered接口，因此我们可以将此切面的优先级设置得比事务管理切面的优先级高，这样每次重试，都可以保证有一个新的事务。（这句话的意思是Around advice与After advice一样，优先级越高，越后执行。）切面中有两个属性：maxRetries和order，它们的值都可通过Spring注入。切面重试的逻辑在doConcurrentOperation()方法中实现，此Around advice在所有”*businessService()*”匹配的连接点都会执行。如果执行次数超出最大限制，才会抛出PessimisticLockingFailureException。

上面切面对应在Spring中的配置如下：

<aop:aspectj-autoproxy/>

 

<bean id="concurrentOperationExecutor"class="com.xyz.myapp.service.impl.ConcurrentOperationExecutor">

​    <property name="maxRetries" value="3"/>

​    <property name="order" value="100"/>

</bean>

如果我们只想对幂运算进行重试操作，我们可以定义一个@Idempotent注解，然后改进上面的切面。如下，首先定义一个注解：

*@Retention(RetentionPolicy.RUNTIME)*

**public** *@interface* Idempotent {

​    *// marker annotation*

}

然后用@Idempotent标注实现业务操作的方法。

最后修改切面，增加切入点表达式限制：匹配的连接点，只有被@Idempotent标注，才切入重试advice。代码如下：

*@Around("com.xyz.myapp.SystemArchitecture.businessService()&& " +*

*       "@annotation(com.xyz.myapp.service.Idempotent)")*

**public** Object doConcurrentOperation(ProceedingJoinPoint pjp) **throws** Throwable {

​    ...

}

# **9.3基于Schema的AOP支持（Schema-based AOP surpport）**

如果你更喜欢用XML来实现配置，Spring提供的aop命名空间标签刚好可以满足此需要。基于Shema的AOP支持，与基于@AspectJ注解的AOP支持，二者的基本组件、基本概念是一样（如切入点表达式、advice有哪些类型等），那些相同的概念本部分就不介绍了。我们将焦点放在二者使用时的语法区别上。

在本节开始前，要使用aop命名空间标签，你首先要在配置文件中导入spring-aop schema。Schema的类型有很多，如：lang、jms、tx、aop、context等等。这里就不一一张贴了。可以参考Sping4相关文档的34章。

用spring配置切面时，所有的aspect、advisor都要放在<aop:config>标签中。当然，一个应用容器中，你可以配置多个<aop:config>。一个<aop:config>可以包含以下元素，且它们是有序的，顺序为：pointcut、advisor、aspect。

*注意：<aop:config>标签对Spring的自动代理机制有很强的依赖。如果你在应用容器中已经指定了一个自动代理工厂（如：BeanNameAutoProxyCreator、DefaultAdvisorAutoProxyCreator、AbstractAdvisorAutoProxyCreator），此时再使用<aop:config>就可能会出现问题。因此，我们推荐<aop:config>和AutoProxyCreator，只能选其一使用。*

## **9.3.1定义切面**

基于schema的切面定义，切面与一个java普通类的写法一样。切面中可以定义切入后的属性和行为，而切入点表达式、advice名称、advice类型等则在XML中指定。

切面的定义使用<aop:aspect>标签，标签属性id为切面命名，属性ref指向切面类生成的bean。示例：

<aop:config>

​    <aop:aspect id="myAspect" ref="aBean">

​        ...

​    </aop:aspect>

</aop:config>

 

<bean id="aBean" class="...">

​    ...

</bean>

上面的aBean就是指切面类生成的bean，aBean可以像spring的其他bean一样被配置，被注入。

## **9.3.2定义切入点**

切入点使用标签<aop:pointcut>定义。首先看一个例子，该切入点匹配service包目录，当前目录中定义的所有类的所有方法：

```
<aop:config>
```

```
    <aop:pointcut id="businessService"        expression="execution(* com.xyz.myapp.service..(..))"/>
```

```
</aop:config>
```

上面的切入点表达式的语法与9.2中的讨论时一样的。如果你的schema是基于申明的，你还可以引用另一个切入点的签名来定义字节的切入点表达式。如下：

```
<aop:config>
```

```
    <aop:pointcut id="businessService"        expression="com.xyz.myapp.SystemArchitecture.businessService()"/>
```

```
</aop:config>
```

这里假设你有一个注解型的（@Aspect）切面：SystemArchitecture，切面中定义了一个切入点（@PointCut）：public voidbusinessService()。让后上面我们就可以引用该切入点了。

在<aop:aspect>标签中定义一个切入点，与在aop顶级标签<aop:config>中定义切入点很相似。如下：

```
<aop:config>
```

```
    <aop:aspect id="myAspect" ref="aBean">
```

```
        <aop:pointcut id="businessService"
```

```
            expression="execution(* com.xyz.myapp.service..(..))"/>
```

```
        ...
```

```
    </aop:aspect>
```

```
</aop:config>
```

与@AspectJ注解申明的切面一样，Schema申明的切面也可以通过&&、||、!来连接切入点表达式。如下，是两个切入点的交集：

```
<aop:config>
```

```
    <aop:aspect id="myAspect" ref="aBean">
```

```
        <aop:pointcut id="businessService"
```

```
            expression="execution(* com.xyz.myapp.service..(..)) &amp;&amp; this(service)"/>
```

```
        <aop:before pointcut-ref="businessService" method="monitor"/>
```

```
        ...
```

```
    </aop:aspect>
```

```
</aop:config>
```

上面的代码中Beforeadvice方法参数就需包含必要的参数，如下：

```
public void monitor(Object service) {
```

```
    ...
```

```
}
```

当在xml中连接切入点子表达式时，&符合很不好处理。我们可以用and、or、not来替代&&、||、!。如，上面的切入点可以重写为：

```
<aop:config>
```

```
    <aop:aspect id="myAspect" ref="aBean">
```

```
        <aop:pointcut id="businessService"
```

```
            expression="execution(* com.xyz.myapp.service..(..)) and this(service)"/>
```

```
        <aop:before pointcut-ref="businessService" method="monitor"/>
```

```
        ...
```

```
    </aop:aspect>
```

```
</aop:config>
```

注意：上面的and关键字有个限制。上面定义切入点的方式与XML的id有关，如果你在切入点表达式要组合另一个切入点的签名，就不能用上面的方式。使用切入点签名，基于schema的方式比基于@AspectJ注解的方式有更多的限制。

## **9.3.3定义通知（advice）**

与@AspectJ注解中的通知一样，5种类型，名称相同，功能相同。

### **Before-advice**

申明在<aop:aspect>标签中，用<aop:before>定义：

```
<aop:aspect id="beforeExample" ref="aBean">
```

```
    <aop:before
```

```
        pointcut-ref="dataAccessOperation"
```

```
        method="doAccessCheck"/>
```

```
    ...
```

```
</aop:aspect>
```

上面的dataAccessOperation是定义在顶级标签（<aop:config>）中的切入点id，如果你想在advice自己的切入点，可以将pointcut-ref属性替换为pointcut属性，修改示例如下：

```
<aop:aspect id="beforeExample" ref="aBean">
```

```
    <aop:before
```

```
        pointcut="execution(* com.xyz.myapp.dao..(..))"
```

```
        method="doAccessCheck"/>
```

```
    ...
```

```
</aop:aspect>
```

上面pointcut中的属性method定义了一个方法（doAccessCheck），此属性指明了advice的方法体。doAccessCheck方法必须在切面类中定义。当一个数据访问操作连接点与切入点规则匹配，那么doAccessCheck方法回执连接点之前执行。

### **After-Returning advice**

After-returning advice在连接点正常执行后调用，定义标签<aop:after-returning>，是<aop:aspect>的子元素。

<aop:aspect id="afterReturningExample" ref="aBean">

​    <aop:after-returning

​        pointcut-ref="dataAccessOperation"

​        method="doAccessCheck"/>

​    ...

</aop:aspect>

与@AspectJ定义一样，XML中可以在advice方法体中定义的返回值。使用returning属性指定需要传递的返回值参数名称。

<aop:aspect id="afterReturningExample" ref="aBean">

​    <aop:after-returning

​        pointcut-ref="dataAccessOperation"

​        returning="retVal"

​        method="doAccessCheck"/>

​    ...

</aop:aspect>

如果你的advice要访问返回值，那在advice方法doAccessCheck就必须包含一个名为retVal的参数。retVal的类型也会限制连接点的匹配，与@AspectJ一样。doAccessCheck示例如下：

**public void** doAccessCheck(Object retVal) {...}

### **After-throwing advice**

捕获抛出特定异常的连接点，执行此advice。定义标签<aop:after-throwing>，也是<aop:aspect>的子标签。

<aop:aspect id="afterThrowingExample" ref="aBean">

​    <aop:after-throwing

​        pointcut-ref="dataAccessOperation"

​        method="doRecoveryActions"/>

​    ...

</aop:aspect>

与@AspectJ定义一样，此类型的advice可以定义指定的异常类型。如下：

<aop:aspect id="afterThrowingExample" ref="aBean">

​    <aop:after-throwing

​        pointcut-ref="dataAccessOperation"

​        throwing="dataAccessEx"

​        method="doRecoveryActions"/>

​    ...

</aop:aspect>

如果定义如上，那么doRecoveryActions方法就必须申明包含一个dataAccessEx参数。当然异常类型也会成为匹配连接点的一个条件。aoRecoveryActions方法如下：

**public void** doRecoveryActions(DataAccessExceptiondataAccessEx) {...}

### **After(Finally) advice**

无论连接点方法执行是否正常，调用此advice。定义标签<aop:after>，<aop:aspect>子标签。

<aop:aspect id="afterFinallyExample" ref="aBean">

​    <aop:after

​        pointcut-ref="dataAccessOperation"

​        method="doReleaseLock"/>

​    ...

</aop:aspect>

### **Around advice**

Around advice作用在@AspectJ类型的定义中已经描述过，此处不重复，如有遗忘可以回顾@AspecJ支持的Around advice部分。

Around advice定义的标签<aop:around>，也是<aop:aspect>的子标签。Advice方法的第一个参数必须是ProceedingJoinPoint，在方法体内通过ProceedingJoinPont.proceed()方法来回调连接点方法。Proceed()方法也可以传递Object[]参数数组，具体可以参考@AspectJ支持的Aroundadvice部分——calling proceed with an Object[].

<aop:aspect id="aroundExample" ref="aBean">

​    <aop:around

​        pointcut-ref="businessService"

​        method="doBasicProfiling"/>

​    ...

</aop:aspect>

doBasicProfiling方法体定义如下：

**public** Object doBasicProfiling(ProceedingJoinPoint pjp) **throws** Throwable {

​    *// start stopwatch*

​    Object retVal = pjp.proceed();

​    *// stop stopwatch*

​    **return** retVal;

}

### **Advice参数**

通过切入点参数的名称来匹配advice方法参数，使得基于schema的AOP申明支持所有我们在上面@AspectJ中讨论过的advice类型。具体可以参看@AspectJ的advice参数部分。如果你想明确的指定advice方法的参数名称（而不是上面提到过的嗅探策略绑定参数），那么你需要定义advice标签中的arg-names属性。Arg-names属性与@AspectJ中advice注解属性”argNames”功能相同，如果你忘了或者跳过了那节，可以参考@AspectJ中的“检查参数名称”部分。先举个简单的例子：

<aop:before

​    pointcut="com.xyz.lib.Pointcuts.anyPublicMethod() and@annotation(auditable)"

​    method="audit"

​    arg-names="auditable"/>

这里的auditable就是参数名称。如果你有多个参数，arg-names中的值可以用英文逗号分隔。

下面我们用基于Schema的方式实现一个稍微复杂点的例子，例子中是Around advice，它的参数类型被明确指定。首先，我们模拟一个业务接口和实现类：

**package** x.y.service;

//接口

**public interface** FooService {

 

​    Foo getFoo(String fooName, **int** age);

}

//对应实现类

**public class** DefaultFooService **implements** FooService {

 

​    **public** Foo getFoo(String name, **int** age) {

​        **return new** Foo(name, age);

​    }

}

然后是Around advice。注意，advice方法（profile(..)）的参数也指明了参数类型，那么当我们用proceed()回调getFoo()方法前，这两个参数可以首先被advice方法体调用：

**package** x.y;

 

**import** org.aspectj.lang.ProceedingJoinPoint;

**import** org.springframework.util.StopWatch;

 

**public class** SimpleProfiler {

 

​    **public** Object profile(ProceedingJoinPoint call,String name, **int** age) **throws** Throwable {

​        StopWatch clock = **new** StopWatch("Profiling for *'" + name + "*' and *'" + age + "*'");

​        **try** {

​           clock.start(call.toShortString());

​            **return** call.proceed();

​        } **finally** {

​            clock.stop();

​           System.out.println(clock.prettyPrint());

​        }

​    }

}

其次，是XML配置。入下：

<beans xmlns="http://www.springframework.org/schema/beans"

​    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

​    xmlns:aop="http://www.springframework.org/schema/aop"

​    xsi:schemaLocation="

​       http://www.springframework.org/schema/beanshttp://www.springframework.org/schema/beans/spring-beans.xsd

​        http://www.springframework.org/schema/aophttp://www.springframework.org/schema/aop/spring-aop.xsd">

 

​    *<!-- this is the object that will beproxied by Spring's AOP infrastructure -->*

​    <bean id="fooService" class="x.y.service.DefaultFooService"/>

 

​    *<!-- this is the actual advice itself-->*

​    <bean id="profiler" class="x.y.SimpleProfiler"/>

 

​    <aop:config>

​        <aop:aspect ref="profiler">

<!-- 注意expression中getFoo中是参数类型 -->

​            <aop:pointcut id="theExecutionOfSomeFooServiceMethod"

​                expression="execution(* x.y.service.FooService.getFoo(String,int))

​                andargs(name, age)"/>

 

​            <aop:around pointcut-ref="theExecutionOfSomeFooServiceMethod"

​                method="profile"/>

 

​        </aop:aspect>

​    </aop:config>

 

</beans>

最后，测试。自定义一个测试类，如下：

**import** org.springframework.beans.factory.BeanFactory;

**import** org.springframework.context.support.ClassPathXmlApplicationContext;

**import** x.y.service.FooService;

 

**public final class** Boot {

 

​    **public static void** main(**final** String[] args) **throws** Exception {

​        BeanFactory ctx = **new** ClassPathXmlApplicationContext("x/y/plain.xml");

​        FooService foo =(FooService) ctx.getBean("fooService");

​        foo.getFoo("Pengo", 12);

​    }

}

你会看到如下的输出打印：

StopWatch *Profiling for 'Pengo* and *12*':running time (millis) = 0

\-----------------------------------------

ms    %     Task name

\-----------------------------------------

00000 ?  execution(getFoo)

 

### **Advice顺序**

当多个相同类型的advice都要切入同一个连接点时，其执行顺序的机制与@AspectJ中的讨论一致。指定advice的优先级也一样，可以通过@Order注解或实现Ordered接口来实现。

## **9.3.4引入**

引入的概念在@AspecJ中已经讨论过，这里不再重复。

引入的定义采用标签<aop:declare-parents>，它也是<aop:aspect>的子标签。这个标签说明匹配的类型将有一个新的父类。例如，给定一个接口UsageTracked，和其实现类DefaultUsageTracked，下面的切面申明，所有实现了service接口的实现类也都将实现UsageTracked接口。

<aop:aspect id="usageTrackerAspect" ref="usageTracking">

 

​    <aop:declare-parents

​        types-matching="com.xzy.myapp.service.*+"

​        implement-interface="com.xyz.myapp.service.tracking.UsageTracked"

​        default-impl="com.xyz.myapp.service.tracking.DefaultUsageTracked"/>

 

​    <aop:before

​        pointcut="com.xyz.myapp.SystemArchitecture.businessService()

​            andthis(usageTracked)"

​            method="recordUsage"/>

 

</aop:aspect>

对于usageTrackingbean的支撑类，应该包含下面的方法：

**public void** recordUsage(UsageTracked usageTracked) {

   usageTracked.incrementUseCount();

}

<aop:declare-parents>标签中：types-matching属性定义要引入的接口；default-impl属性定义引入接口的实现类；types-matching属性采用了AspectJ类型的匹配方式：所有符合匹配条件的类都将实现UsageTracked接口。

当上门的定义都生效以后，你可以这样使用UsageTracked接口的功能（向上转型为其中的一个父类）：

UsageTracked usageTracked = (UsageTracked) context.getBean("myService");

 

## **9.3.5Aspect实例化模型**

基于Schema的Aspect的实例化模型只有一种：单例模式。所有这是Schema-based与@AspectJ-based支持的一个重要区别。

## **9.3.6 Advisors**

Advisors的概念是从Spring 1.2关于AOP的定义中借鉴过来的，因此在@AspectJ中没有直接的对应功能。一个Advisor是一个很小的自包含切面，此切面只有一个advice。这种advice用spring中的一个bean来表示，而且必须实现advice接口（org.springframework.aop包下的AfterReturningAdvice、MethodBeforeAdvice、ThrowsAdvice等接口），然后切入点表达式来匹配连接点。

Advisor在spring中用标签<aop:advisor>来定义，我们经常在配置处理事务通知的时候看到它是身影。下面就是这样的例子（关于事务的标签请参考相关部分）：

<aop:config>

​    <aop:pointcut id="businessService"

​        expression="execution(* com.xyz.myapp.service.**.**(..))"/>

​    <aop:advisor

​        pointcut-ref="businessService"

​        advice-ref="tx-advice"/>

</aop:config>

 

<tx:advice id="tx-advice">

​    <tx:attributes>

​        <tx:method name="*" propagation="REQUIRED"/>

​    </tx:attributes>

</tx:advice>

上面的pointcut-ref属性与前面的的功能一样，你也可以在<aop:advisor>标签中用pointcut属性定义一个自己的切入点表达式。

Advisor也可以指定指定优先级，此属性为order。

## **9.3.7几个栗子**

现在基于schema的aop支持差不多介绍完了。我们将上面@AspectJ中的栗子用Schema的方式重写。

还是简单描述下需求：服务层执行业务逻辑时，有时可能会由于并发问题导致失败（如死锁）。很多并发问题后，可以进行一次重试，也许问题就能解决。这种重试的处理方式对服务层业务就很适用（如幂运算，你不需要因并发错误，将错误冲突返回给用户来解决）。很明显，我们更乐意重新执行一次业务逻辑，而不是给用户返回一个PessimisticLockingFailureException异常。对于这种情况，我们需要切入很多个服务层业务类来编写重试的代码，那么切面编程就是一个很好的主义。

因为我们要对失败的方法进行重试，那么可以选择Around advice，因为它可以多次调用连接点方法。下面是一个基本的切面实现（基于Schema的切面就是一个简单java类）：

**public class** ConcurrentOperationExecutor **implements** Ordered {

 

​    **private static final int** DEFAULT_MAX_RETRIES = 2;

 

​    **private int** maxRetries = DEFAULT_MAX_RETRIES;

​    **private int** order = 1;

 

​    **public void** setMaxRetries(**int** maxRetries) {

​        **this**.maxRetries = maxRetries;

​    }

​    **public int** getOrder() {

​        **return this**.order;

​    }

​    **public void** setOrder(**int** order) {

​        **this**.order = order;

​    }

 

​    **public** ObjectdoConcurrentOperation(ProceedingJoinPoint pjp) **throws** Throwable {

​        **int** numAttempts = 0;

​       PessimisticLockingFailureException lockFailureException;

​        **do** {

​            numAttempts++;

​            **try** {

​                **return** pjp.proceed();

​            }

​            **catch**(PessimisticLockingFailureException ex) {

​                lockFailureException= ex;

​            }

​        } **while**(numAttempts <= **this**.maxRetries);

​        **throw** lockFailureException;

​    }

}

//这个代码与@AspectJ栗子的代码完全一样，仅仅是将@Annotation去掉了

上面的例子，切面实现了Ordered接口，因此我们可以将此切面的优先级设置得比事务管理切面的优先级高，这样每次重试，都可以保证有一个新的事务。（这句话的意思是Around advice与After advice一样，优先级越高，越后执行。）切面中有两个属性：maxRetries和order，它们的值都可通过Spring注入。切面重试的逻辑在doConcurrentOperation()方法中实现，此Around advice在所有“businessService()”匹配的连接点都会执行。如果执行次数超出最大限制，才会抛出PessimisticLockingFailureException。

上面切面对应在Spring中的配置如下：

<aop:config>

​    <aop:aspect id="concurrentOperationRetry" ref="concurrentOperationExecutor">

​        <aop:pointcut id="idempotentOperation"

​            expression="execution(* com.xyz.myapp.service.**.**(..))"/>

​        <aop:around

​            pointcut-ref="idempotentOperation"

​            method="doConcurrentOperation"/>

​    </aop:aspect>

</aop:config>

 

<bean id="concurrentOperationExecutor"    class="com.xyz.myapp.service.impl.ConcurrentOperationExecutor">

​        <property name="maxRetries" value="3"/>

​        <property name="order" value="100"/>

</bean>

如果我们只想对幂运算进行重试操作，我们可以定义一个@Idempotent注解，然后改进上面的切面。如下，首先定义一个注解：

*@Retention(RetentionPolicy.RUNTIME)*

**public** *@interface* Idempotent {

​    *// marker annotation*

}

然后用@Idempotent标注实现业务操作的方法。

最后修改切面，增加切入点表达式限制：匹配的连接点，只有被@Idempotent标注，才切入重试advice。配置如下：

<aop:pointcut id="idempotentOperation"

​        expression="execution(* com.xyz.myapp.service.**.**(..)) and

​       @annotation(com.xyz.myapp.service.Idempotent)"/>

 

# **9.4 AOP定义方式比较**

如果你觉得采用AOP来实现一些特定的需求，那么众多的AOP定义方式：Spring AOP、AspectJ、Aspect代码实现、@AspectJ注解、spring XML实现，又该如何选择？这取决于应用需求、开发工具、团队对AOP的熟悉程度。

## **9.4.1 Spring AOP or full AspectJ?**

使用最简单的实现方式。SpringAOP与full AspectJ相比要容易使用，因为你在开发中不需要引入AspectJ编译器和weaver。如果你的通知是要执行在Spring bean上，那Spring AOP是很好的选择。如果你的通知对象不是被Spring容器管理的（最典型的是domain对象），那你可能就需要选择AspectJ了。此外，如果你的连接点匹配不只局限于方法（如属性的set和get），那么你任然需要选择AspectJ。

如果使用AspectJ，你可以选择使用 AspectJ language syntax（也就是用代码实现）或者 @AspectJ annotation style。显然，如果你的java版本低于1.5，那你需要是用代码实现。如果你的框架设计很依赖于切面，你可能会使用AspectJ Development Tool（AJDT）eclipse插件，那么你应该选择AspectJ language syntax：因为这种语言专门设计来实现AOP功能，所有简单清晰。如果你没使用eclipse，或者切面在你代码中只是很小的一部分，那你可以考虑用@AspectJ注解方式。

## **9.4.2 @AspectJ or XMl for Apring AOP?**

如果你选择Spring AOP，那你还需要考虑是用@AspectJ注解还是基于XMl来实现需求。

XML方式对应使用过Spring用户来说是最熟悉不过了。如果将AOP作为一种工具来配置企业级应用，那XMl非常合适。（如果你也不清楚是否合适，你可以从切入点表达式入手：切入点表达式是否是你配置的一部分？如果是，此部分用XML配置可以单独修改）。同时，使用XML配置AOP，可以很清晰的看到应用中所用的切面。

但XMl-style有两个缺点：第一，XML-style对需求实现封装不完全，同一个需求的所有实现并未封装在一个地方。DRY（don’t repeat yourslfe）原则说，你的需求在你的系统中应该是单一、不耦合、有代表性的存在。而当你使用了XML-style，那你的需求是如何实现的这一概念就被切面的支撑类切分了，如果单独看切面类，你对此类应该发挥的作用完全摸不着北，因为切入点被定义在了XML中。而当你使用@AspectJ-style，注解写在切面类中，所有AOP的必要信息都被封装在进了切面。第二，XMl-style比起@AspectJ-style还有一些局限：首先，XML-style只支持单例形式；其次，XML中定义切入点不能引用其它切入点名称（id）。比如，在@AspectJ-style中，我们可以这样定义切入点：

*@Pointcut(execution(\* get*()))*

**public void** propertyAccess() {}

 

@Pointcut(execution(org.xyz.Account+ *(..))

**public void** operationReturningAnAccount() {}

 

*@Pointcut(propertyAccess() && operationReturningAnAccount())*

**public void** accountPropertyAccess() {}

而在XML中我们只能定义前两个切入点实现：

<aop:pointcut id="propertyAccess"

​        expression="execution(* get*())"/>

 

<aop:pointcut id="operationReturningAnAccount"

​        expression="execution(org.xyz.Account+ *(..))"/>

第三个accountPropertyAccess切入点，在XMl中就不能做类似的组合引用。

总结一下，@AspectJ-style支持更多的实例化模型、更多的切入点表达式组合方式，可以让切面信息封装得更好，同时其语法兼容Spring AOP和AspectJ——如果后来需要让自己的系统兼容AspectJ，这很容易实现从Spring AOP迁移到AspectJ实现上。所以，Spring团队更倾向于使用@AspectJ-based实现AOP，因为无论何时，它都比XML-based类型容易扩展。

# **9.5 混合使用切面类型**

在同一个配置中定义：@AspectJ切面、<aop:aspect>切面、<aop:advisor>、甚至是Spring1.2中的代理和拦截器，都是可以的。因为这些实现都基于相同的底层机制，所以他们理所当然，可以共存。

# **9.6 代理机制**

Spring用JDK动态代理和CGLIB来为一个指定类（target）生成代理。（JDK动态代理是首选）。

如果target实现类一个或一个以上的接口，那么会调用JDK动态代理来生成代理。所有的接口都会被代理。而若target没有实现任何类，那么会调用CGLIB来生成代理。

如果你想强制使用CGLIB（如，代理target中的所有方法，而不仅仅是接口中定义的方法），需要先注意以下几点：

l Final方法不能被代理，因为它不能被重写；

l Spring 3.2以后，CGLIB包不需要在单独导入，因为其已直接打包进了spring框架的spring-core.jar中。这意味着CGLIB-based代理与JDK-based代理有着一致的工作方式。

l 你的被代理类的构造方法会被调用两次。这是CGLIB代理模型自然产生的结果，因为每个代理类生成子类。对于每个代理实例，都会创建两个对象：一个真实代理对象和一个实现了advice的子类对象。这种行为在JDK-based中是不会发生的。通常，调用被代理类的构造方法两次，不会产生什么问题，因为我们一般不会在构造方法中指派任务和逻辑实现。

现在，我们来看看使用<aop:config>标签的属性proxy-target-class属性来指定CGLIB-based代理实现：

<aop:config proxy-target-class="true">

​    *<!-- other beans defined here... -->*

</aop:config>

在@AspectJ注解中使用CGLIB-based代理，将<aop:aspectj-autoproxy>标签的’proxy-target-class’属性设为true：

<aop:aspectj-autoproxy proxy-target-class="true"/>

## **9.6.1 理解AOP代理**

从这节的AOP部分往下就不翻译了，因为可能很少用，我记几个标题，以后如果用到在查Spring相关文档。

自己总结的AOP：要想理解AOP代理，建议首先先学一下代理模式，当然主要是动态代理部分，因为Spring AOP就是基于动态代理实现的。对照着动态代理实现原理，很容易理解AOP代理。可以参考我的另一篇笔记：代理模式之学习笔记。

# **9.7 用代码编写的方式实现@AspectJ代理**

略

# **9.8在Spring应用中使用AspectJ**

略

## **9.8.1在Spring中使用AspectJ注入domain对象**

略

## **9.8.2 Spring中支持AspectJ的其他切面**

略

## **9.8.3 使用Spring IoC配置AspectJ切面**

略

## **9.8.4 Spring Framework代码加载时用AspectJ织入**

略

# **9.9 了解更多的信息**

略
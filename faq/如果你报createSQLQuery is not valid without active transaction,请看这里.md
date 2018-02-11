# 如果你报createSQLQuery is not valid without active transaction,请看这里

原创 2013年03月13日 09:35:43

- 标签：
- [java](http://so.csdn.net/so/search/s.do?q=java&t=blog) /
- [ibm](http://so.csdn.net/so/search/s.do?q=ibm&t=blog)


- **44578

很多时候我们使用hibernate的session时，都是让session在某一运行环境中保持其唯一。例如在同一线程内用同一个session，在同一方法内用同一session，这样我们就可以用session里面缓存好的数据。但，我想说的不是缓存，且听我一一道来。
​        最近试用spring3.0.2+struts2.18+hibernate3.3.2学习搭建一个web项目，出现了一个相当郁闷的问题。就是我明明配置好了spring管理hibernate事务了，**当我在dao中执行hibernate的方法时，如save，delete，update，createQuery，总是说不能在没有活动的事务中执行（org.hibernate.HibernateException: createSQLQuery is not valid without active transaction）。**立马上google查，一无所获。曾几度怀疑是否配置写出了，dao或service写错了，改来改去的依旧存在问题。当时相当郁闷啊，想啊，你spring不是帮我管理事务么？你不自动开启事务啊，还要我手动开启啊。立马查spring文档，从中文到英文，没发现什么有参考价值的线索，真是相当的打击。代码乱改一通，发现用spring的hibernatetemplate来进行数据操作又正常无比。不死心的去查了hibernate的doc，一个不留神给哥发现了一个冗长的配置属性：hibernate.current_session_context_class。心里巨爽无比，就是你丫啦。小样的，哥把你灭了。

通俗点来讲，就是配置session绑定到某一运行环境，例如从同一个线程中用getCurrentSession()取得的session都是同一个，当前没有session就自动创建一个返回给你丫用。

​        使用 Hibernate 的大多数应用程序需要某种形式的“上下文相关的”会话，特定的会话在整个特
定的上下文范围内始终有效。然而，对不同类型的应用程序而言，要为什么是组成这种“上下文”下一个定义通常是困难的；不同的上下文对“当前”这个概念定义了不同的范围。**在 3.0 版本之前，使用 Hibernate 的程序要么采用自行编写的基于 ThreadLocal 的上下文会话，要么采用HibernateUtil 这样的辅助类，要么采用第三方框架（比如 Spring 或 Pico），它们提供了基于代理（proxy）或者基于拦截器（interception）的上下文相关的会话。从 3.0.1 版本开始，Hibernate 增加了SessionFactory.getCurrentSession() 方法。**一开始，它假定了采用 JTA 事务，JTA 事务定义了当前 session 的范围和上下文（scope 和 context）。因为有好几个独立的 JTA TransactionManager 实现稳定可用，不论是否被部署到一个 J2EE 容器中，
大多数（假若不是所有的）应用程序都应该采用 JTA 事务管理。基于这一点，采用 JTA 的上下文相关的会话可以满足你一切需要。
​        再来看我的配置,讲hibernate.current_session_context_class的值设成thread。按我简单的理解就是将getCurrentSession()返回的session绑定到当前运行线程中。比较专业的说法是此session的上下文是thread，但不是spring已经托管的那个Session对象。再用哥那大腿想了几下，瞬间了解了一些。所以获取的session是在spring代理的上下文之外的的当前线程之中，所以此session并非事务管理器代理的那个session，不会自动开启事务。根据官方提示：第三方框架提供了基于代理（proxy）或者基于拦截器（interception）的上下文相关的会话的管理，所以把**hibernate.current_session_context_class设置删除**了，一切又回到当初风平浪静的日子了。

参考<http://justsee.iteye.com/blog/1061576>，终于了解这个问题的前因后果。摘录如下：

****

**在ssh2中的sessionFactory配置文件中应将hibernate.current_session_context_class设为org.springframework.orm.hibernate3.SpringSessionContext（默认为此值），并应用spring管理事务。**

**如果为<prop key="hibernate.current_session_context_class">thread</prop> 则会报异常，**

原因还是spring中hibernate.current_session_context_class的问题

在spring的类LocalSessionFactoryBean源码，方法buildSessionFactory中将hibernate.current_session_context_class设为org.springframework.orm.hibernate3.SpringSessionContext
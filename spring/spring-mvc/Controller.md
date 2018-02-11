# Controller

## 18.3.1使用@Controller定义控制器

> @Controller`注释作为注释类的构造型，表示其作用。 调度程序扫描这些注释类的映射方法，并检测`@RequestMapping`注释（请参阅下一节）。
>
> 您可以使用调度程序上下文中的标准Spring bean定义来明确定义带注释的控制器bean。 但是，`@Controller`构造型还允许自动检测，与Spring通用支持对齐，用于检测类路径中的组件类并自动注册它们的bean定义。要启用自动检测这些带注释的控制器，您可以向组态添加组件扫描。 使用spring-context模式，如以下XML代码片段所示：
>
> ```xml
>  <context:component-scan base-package="org.springframework.samples.petclinic.web"/>
>
>   <!-- 视图查找器 根据视图名来匹配实际的视图文件 -->
>     <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
>         <property name="prefix" value="/WEB-INF/jsp/"/>
>         <property name="suffix" value=".jsp"/>
>         <property name="contentType" value="UTF-8"/>
>         <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
>     </bean>
>
> ```

## 18.3.2使用@RequestMapping映射请求

您可以使用`@RequestMapping`注释将诸如`/appointments`的URL映射到整个类或特定的处理程序方法。 通常，类级注释将特定的请求路径（或路径模式）映射到表单控制器上，其他方法级注释缩小了特定HTTP请求方法（“GET”，“POST”等）的主映射，或 HTTP请求参数条件。

```java
@Controller
@RequestMapping("/")
public class HomeController {
    Logger log = LoggerFactory.getLogger(HomeController.class);

    @RequestMapping(method = RequestMethod.GET,value = {"/","/home"})
    public String homePage(){
        return "home";
    }

    @RequestMapping(method = RequestMethod.POST,value = {"/","/home"})
    public String postDate(@RequestParam("home-name") String name,
                           @RequestParam("home-age") String age, Model model){
        model.addAttribute("name",name);
        model.addAttribute("age",age);
        log.info("name {} age {}",name,age);
        return "show";
    }
}
```

`@RequestMapping`用在很多地方。 第一个用法是类型（类）级别，这表示此控制器中的所有处理程序方法都相对于`/`路径.

类级别上的`@RequestMapping`不是必需的。 没有它，所有的路径都是绝对的，而不是相对的。 

`@RequestMapping`用在方法级别

因为`@RequestMapping`默认映射所有HTTP方法。 使用`@RequestMapping(method=GET)`或`@GetMapping`来缩小映射。

### 组合@RequestMapping变体

Spring Framework 4.3引入了`@RequestMapping`注释的以下方法级组合变体，有助于简化常见HTTP方法的映射，并更好地表达注释处理程序方法的语义。 例如，`@GetMapping`可以被读取为GET `@RequestMapping`。

- `@GetMapping`
- `@PostMapping`
- `@PutMapping`
- `@DeleteMapping`
- `@PatchMapping`

### @Controller 和AOP 代理

在某些情况下，控制器可能需要在运行时用AOP代理进行装饰。 一个例子是如果您选择在控制器上直接使用`@Transactional`注释。 **在这种情况下，对于控制器，我们建议使用基于类的代理**。 这通常是控制器的默认选项。 但是，如果控制器必须实现不是Spring Context回调的接口（例如`InitializingBean`，`* Aware`等），则可能需要显式配置基于类的代理。 例如，使用

`<tx：annotation-driven />`，更改为`<tx：annotation-driven proxy-target-class =“true”/>`。

### Spring MVC 3.1中的@RequestMapping方法的新支持类

Spring 3.1分别为`@RequestMapping`方法引入了一组新的支持类，分别叫做`RequestMappingHandlerMapping`和`RequestMappingHandlerAdapter`。

它们被推荐使用，甚至需要利用Spring MVC 3.1中的新功能和未来。默认情况下，MVC命名空间和MVC Java配置启用新的支持类，但是如果不使用，则必须显式配置。本节介绍旧支持类和新支持类之间的一些重要区别

> 在Spring 3.1之前，类型和方法级请求映射在两个单独的阶段进行了检查 – 首先由`DefaultAnnotationHandlerMapping`选择一个控制器，并且实际的调用方法被`AnnotationMethodHandlerAdapter`缩小。
>
> **使用Spring 3.1中的新支持类，`RequestMappingHandlerMapping`是唯一可以决定哪个方法应该处理请求的地方。将控制器方法作为从类型和方法级`@RequestMapping`信息派生的每个方法的映射的唯一端点的集合。**

这使得一些新的可能性。一旦`HandlerInterceptor`或`HandlerExceptionResolver`现在可以期望基于对象的处理程序是`HandlerMethod`，它允许它们检查确切的方法，其参数和关联的注释。 URL的处理不再需要跨不同的控制器进行拆分。

### URI 模版模式 `@PathVariable`

> 可以使用URI模板方便地访问`@RequestMapping`方法中URL的所选部分。
>
> URI模板是一个类似URI的字符串，包含一个或多个变量名称。 当您替换这些变量的值时，模板将成为一个URI。 所提出的RFC模板RFC定义了URI如何参数化。 例如，URI模板`http://www.example.com/users/{userId}`包含变量userId。 将`fred`的值分配给变量会得到`http://www.example.com/users/fred`。

```java
  @GetMapping("/home/{name}")
    public String showName(@PathVariable/*("name")*/ String name/*username*/, Model model) {
        if (name == null)
            name = "";
        User o = (User) userRepository.get(name);
        if (o == null)
            model.addAttribute("error", String.format("Not find User : {}", name));
        else
            model.addAttribute("name", name);
        return "show";
    }
```

一个方法可以有任何数量的`@PathVariable`注释

当在`Map <String，String>`参数上使用`@PathVariable`注释时，映射将填充所有URI模板变量。

#### 具有正则表达式的URI模板模式

`@RequestMapping`注释支持在URI模板变量中使用正则表达式。 语法是`{varName：regex}`，其中第一部分定义了变量名，第二部分定义了正则表达式。

#### 路径模式

除了URI模板之外，`@RequestMapping`注释和所有组合的`@RequestMapping`变体也支持Ant样式的路径模式（例如`/myPath/*.do`）。 还支持URI模板变量和Ant-style glob的组合（例如`/owners/*/pets/{petId}`）。

#### 路径模式比较

当URL匹配多个模式时，使用排序来查找最具体的匹配。

具有较低数量URI变量和通配符的模式被认为更具体。 例如/`hotels/{hotel}/*`具有1个URI变量和1个通配符，被认为比`/hotels/{hotel}/**`更具体，其中1个URI变量和2个通配符。

如果两个模式具有相同的计数，那么较长的模式被认为更具体。 例如`/foo/bar*`比较长，被认为比`/foo/*`更具体。

当两个模式具有相同的计数和长度时，具有较少通配符的模式被认为更具体。 例如`/hotels/ {hotel}`比`/hotels/*`更具体。

下面有些额外增加的特殊的规则：

- **默认映射模式**`/**`比任何其他模式都要小。 例如`/api/{a}/{b}/{c}`更具体。
- 诸如`/public/**`之类的**前缀模式**比不包含双通配符的任何其他模式都不那么具体。 例如`/public/path3/{a}/{b}/{c}`更具体。



#### 具有占位符的路径模式 ***【重要】

`@RequestMapping`注释中的模式支持对本地属性and/or系统属性和环境变量的`${…}`占位符。 在**将控制器映射到的路径可能需要通过配置进行定制的情况下，这可能是有用的**。 有关占位符的更多信息，请参阅`PropertyPlaceholderConfigurer`类的javadocs。

1. 配置`PropertyPlaceholderConfigurer`

   ```xml
       <context:property-placeholder     file-encoding="UTF-8" ignore-unresolvable="true" location="classpath:spring/properties/*-ph.properties"/>
   ```

2. 在`@RequestMapping`中，使用`${..}`引用属性

   ```java
   @Controller
   @RequestMapping("${homeroot}")
   public class HomeController {
   ```

3. 属性文件内容

   ```properties
   homeroot=/
   homepage=/home
   show=/show
   home-regex=/home/{name:[a-z0-9]+}
   home-path-variable=/home/{name}
   ```

   #### 后缀模式匹配

   默认情况下，Spring MVC执行`".*"`后缀模式匹配，以便映射到`/person`的控制器也隐式映射到`/person.*`。这使得通过URL路径（例如`/person.pdf`,`/person.xml`）可以轻松地请求资源的不同表示。

   后缀模式匹配可以关闭或限制为一组明确注册用于内容协商的路径扩展。通常建议通过诸如`/person/{id}`之类的常见请求映射来减少歧义，其中点可能不表示文件扩展名，例如`/person/joe@email.com` vs `/person/joe@email.com.json`。此外，如下面的说明中所解释的，后缀模式匹配以及内容协商可能在某些情况下用于尝试恶意攻击，并且有充分的理由有意义地限制它们。

   有关后缀模式匹配配置，请参见[Section18.16.11, “Path Matching”](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-config-path-matching)，内容协商配置[Section18.16.6, “Content Negotiation”](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-config-content-negotiation)。

#### 后缀模式匹配和RFD

反射文件下载（RFD）攻击是由Trustwave在2014年的一篇论文中首次描述的。攻击类似于XSS，因为它依赖于响应中反映的输入（例如查询参数，URI变量）。然而，不是将JavaScript插入到HTML中，如果基于文件扩展名（例如.bat，.cmd）双击，则RFD攻击依赖于浏览器切换来执行下载并将响应视为可执行脚本。

**在Spring MVC `@ResponseBody`和`ResponseEntity`方法存在风险，因为它们可以呈现客户端可以通过URL路径扩展请求的不同内容类型。但是请注意，单独禁用后缀模式匹配或禁用仅用于内容协商的路径扩展都可以有效地防止RFD攻击。**

为了全面保护RFD，在呈现响应体之前，Spring MVC添加了`Content-Disposition:inline;filename=f.txt`头来建议一个固定和安全的下载文件。只有当URL路径包含既不是白名单的文件扩展名，也没有明确注册用于内容协商的目的，这是完成的。但是，当URL直接输入浏览器时，可能会产生副作用。

许多常见的路径扩展名默认为白名单。此外，REST API调用通常不是直接用于浏览器中的URL。然而，使用自定义`HttpMessageConverter`实现的应用程序可以明确地注册用于内容协商的文件扩展名，并且不会为此类扩展添加Content-Disposition头。见第18.16.6节[“Content Negotiation”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-config-content-negotiation)。

### 矩阵变量

> 请注意，为了使用矩阵变量，您必须将`RequestMappingHandlerMapping`的`removeSemicolonContent`属性设置为`false`。 默认设置为`true`。
>
> 如果您使用Java配置，使用MVC Java Config的高级自定义部分将介绍如何自定义RequestMappingHandlerMapping。
>
> 在MVC命名空间中，`<mvc：annotation-driven>`元素具有一个应该设置为`true`的`enable-matrix-variables`属性。 默认情况下设置为`false`。
>
> ```xml
>     <mvc:annotation-driven enable-matrix-variables="true"/>
> ```
>
> 

URI规范[RFC 3986](https://tools.ietf.org/html/rfc3986#section-3.3)定义了在路径段中包含名称 – 值对的可能性。规格中没有使用具体术语。可以应用一般的“URI路径参数”，尽管来自Tim Berners-Lee的旧帖子的更独特的["Matrix URIs"](https://www.w3.org/DesignIssues/MatrixURIs.html)也经常被使用并且是相当熟知的。在Spring MVC中，这些被称为矩阵变量。

矩阵变量可以出现在任何路径段中，每个矩阵变量用“;”分隔（分号）。例如：`"/cars;color=red;year=2012"`。多个值可以是“，”（逗号）分隔`"color=red,green,blue"`，或者变量名称可以重复`"color=red;color=green;color=blue"`。

- URL预期包含矩阵变量，则请求映射模式必须使用URI模板来表示它们。这确保了请求可以正确匹配，无论矩阵变量是否存在，以及它们提供什么顺序。

以下是提取矩阵变量“q”的示例：

```java
// GET /pets/42;q=11;r=22

@GetMapping("/pets/{petId}")
public void findPet(@PathVariable String petId, @MatrixVariable int q) {

    // petId == 42
    // q == 11

}
```

- 所有路径段都可能包含矩阵变量，因此在某些情况下，您需要更具体地确定变量预期位于何处：

```java
// GET /owners/42;q=11/pets/21;q=22

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable(name="q", pathVar="ownerId") int q1,
        @MatrixVariable(name="q", pathVar="petId") int q2) {

    // q1 == 11
    // q2 == 22

}
```

- 变量可以定义为可选参数，并指定一个默认值：

```java
// GET /pets/42

@GetMapping("/pets/{petId}")
public void findPet(@MatrixVariable(required=false, defaultValue="1") int q) {

    // q == 1

}
```

- 所有矩阵变量可以在Map中获得：

```java
// GET /owners/42;q=11;r=12/pets/21;q=22;s=23

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable MultiValueMap<String, String> matrixVars,
        @MatrixVariable(pathVar="petId"") MultiValueMap<String, String> petMatrixVars) {

    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 11, "s" : 23]
}
```

### Consumable Media 类型

您可以通过指定consumable media类型的列表来缩小主要映射。 只有当`Content-Type`请求头与指定的媒体类型匹配时，才会匹配该请求。 例如：

```java
@PostMapping(path = "/pets", consumes = "application/json")
public void addPet(@RequestBody Pet pet, Model model) {
    // implementation omitted
}
```

consumable media类型表达式也可以在`!text/plain`中否定，以匹配除`Content-Type` 或 `text/plain`之外的所有请求。 还要考虑使用`MediaType`中提供的常量，例如`APPLICATION_JSON_VALUE`和A`PPLICATION_JSON_UTF8_VALUE`。

### Producible Media 类型

您可以通过指定producible media类型列表来缩小主要映射。 只有当`Accept`请求头匹配这些值之一时，才会匹配该请求。 此外，使用产生条件确保用于产生响应的实际内容类型与产生条件中指定的媒体类型相关。 例如：

```java
@GetMapping(path = "/pets/{petId}", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
@ResponseBody
public Pet getPet(@PathVariable String petId, Model model) {
    // implementation omitted
}
```

> ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/note.png)  在\_products\_condition中指定的媒体类型也可以选择指定一个字符集。 例如，在上面的代码片段中，我们指定与默认配置在`MappingJackson2HttpMessageConverter`中的介质类型相同的介质类型，包括`UTF-8` charset。
>
> 像消费一样，producible media类型表达式可以被否定为`!text/plain`，以匹配除了`Accept`头文件值为`text/plain`的所有请求。 还要考虑使用`MediaType`中提供的常量，例如`APPLICATION_JSON_VALUE`和`APPLICATION_JSON_UTF8_VALUE`。

在类型和方法级别上支持\_\_produces\_condition。 与大多数其他条件不同，当在类型级别使用时，方法级producible类型将覆盖而不是扩展类型级producible类型。

### 请求参数和头部值

您可以通过请求参数条件（如`"myParam"`，`"!myParam"`或`"myParam=myValue"`）缩小请求匹配。 前两个测试请求参数存在/不存在，第三个为特定参数值。 下面是一个请求参数值条件的例子：

```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @GetMapping(path = "/pets/{petId}", params = "myParam=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

也可以根据特定的请求头值来测试请求头存在/不存在或匹配：

```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @GetMapping(path = "/pets", headers = "myHeader=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

| ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/tip.png) |
| :--------------------------------------- |
| 虽然可以使用媒体类型通配符匹配toContent-Type\_and\_Accept\_header值（例如“content-type = text / \*”将匹配“text / plain”和“text / html”\_），但建议分别使用\_onsumes\_and\_produces\_cignitions。 它们专门用于此目的。 |

### HTTP 头部和 HTTP 可选项

映射到“GET”的`@RequestMapping`方法也隐式映射到“HEAD”，即不需要明确声明“HEAD”。处理HTTP HEAD请求就像是HTTP GET一样，而不是仅写入正文，仅计数字节数，并设置“Content-Length”头。

映射到“GET”的`@RequestMapping`方法也隐式映射到“HEAD”，即不需要明确声明“HEAD”。处理HTTP HEAD请求就像是HTTP GET一样，而不是仅写入正文，仅计数字节数，并设置“Content-Length”头。

`@RequestMapping`方法内置支持HTTP选项。默认情况下，通过将所有`@RequestMapping`方法上显式声明的具有匹配URL模式的HTTP方法设置为“允许”响应头来处理HTTP OPTIONS请求。当没有明确声明HTTP方法时，“允许”标题设置为“GET，HEAD，POST，PUT，PATCH，DELETE，OPTIONS”。理想地总是声明`@RequestMapping`方法要处理的HTTP方法，或者使用专用的组合`@RequestMapping`变体之一（参见[“Composed @RequestMapping Variants”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-requestmapping-composed)一节）。

虽然不需要`@RequestMapping`方法可以映射到HTTP HEAD或HTTP选项，也可以两者兼容。

## 18.3.3 定义@RequestMapping 处理方法

`@RequestMapping`处理方法可以有非常灵活的签名。 支持的方法参数和返回值将在以下部分中介绍。 大多数参数可以按任意顺序使用，唯一的例外是`BindingResult`参数。

| ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/note.png) |
| :--------------------------------------- |
| Spring 3.1为`@RequestMapping`方法引入了一组新的支持类，分别称为`RequestMappingHandlerMapping`和`RequestMappingHandlerAdapter`。 它们被推荐使用，甚至需要利用Spring MVC 3.1中的新特性并继续前进。 新的支持类默认从MVC命名空间启用，并且使用MVC Java配置，但是如果两者都不使用，则必须明确配置。 |

#### 支持的方法参数类型

下面是支持的方法参数类型：

- `org.springframework.web.context.request.WebRequest`或 `org.springframework.web.context.request.NativeWebRequest`允许通用请求参数访问以及请求/会话属性访问，而不涉及本机Servlet API。
- Request 或 response 对象\(Servlet API\). 选择任意特定的请求或响应类型, 例如  
  `ServletRequest`或`HttpServletRequest`或 Spring’s`MultipartRequest`/`MultipartHttpServletRequest`  
  .
- Session对象（Servlet API）类型为`HttpSession`。 此类型的参数强制存在相应的会话。 因此，这样的论证从不为`null`。

| ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/note.png) |
| :--------------------------------------- |
| 会话访问可能不是线程安全的，特别是在Servlet环境中。 如果允许多个请求同时访问会话，请考虑将`RequestMappingHandlerAdapter`的“synchronizeOnSession”标志设置为“true”。. |

- `java.servlet.http.PushBuilder`用于关联的Servlet 4.0推送构建器API，允许编程的HTTP / 2资源推送。
- `java.security.Principal`（或一个特定的`Principal`实现类（如果已知）），包含当前验证的用户。
- `org.springframework.http.HttpMethod`为HTTP请求方法，表示为Spring的`HttpMethod`枚举。
- 由当前请求区域设置的`java.util.Locale`，由最具体的语言环境解析器确定，实际上是在MVC环境中配置的`LocaleResolver`/ `LocaleContextResolver`。
- 与当前请求相关联的时区的`java.util.TimeZone`（Java 6+）/ `java.time.ZoneId`（Java 8+），由`LocaleContextResolver`确定。
- `java.io.InputStream` / `java.io.Reader`，用于访问请求的内容。该值是由Servlet API公开的原始InputStream / Reader。
- `java.io.OutputStream` / `java.io.Writer`用于生成响应的内容。该值是由Servlet API公开的原始OutputStream / Writer。
- `@PathVariable`注释参数，用于访问URI模板变量。请参阅[the section called “URI Template Patterns”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-requestmapping-uri-templates)  
  .
- `@MatrixVariable`注释参数，用于访问位于URI路径段中的名称/值对。请参阅 [the section called “Matrix Variables”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-matrix-variables)。
- `@RequestParam`用于访问特定Servlet请求参数的注释参数。参数值将转换为声明的方法参数类型。请参阅[the section called “Binding request parameters to method parameters with @RequestParam”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-requestparam)。
- `@RequestHeader`用于访问特定Servlet请求HTTP标头的注释参数。参数值将转换为声明的方法参数类型。请参阅 [the section called “Mapping request header attributes with the @RequestHeader annotation”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-requestheader)。
- `@RequestBody`用于访问HTTP请求体的注释参数。使用`HttpMessageConverters`将参数值转换为声明的方法参数类型。请参阅[the section called “Mapping the request body with the @RequestBody annotation”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-requestbody)。
- @RequestPart注释参数，用于访问“multipart / form-data”请求部分的内容。请参见[Section 18.10.5, “Handling a file upload request from programmatic clients”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-multipart-forms-non-browsers)和[Section 18.10, “Spring’s multipart \(file upload\) support”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-multipart)
- `@SessionAttribute`用于访问现有的永久会话属性（例如，用户认证对象）的注释参数，而不是通过`@SessionAttributes`作为控制器工作流的一部分临时存储在会话中的模型属性。
- `@RequestAttribute`用于访问请求属性的注释参数。
- `HttpEntity`参数访问Servlet请求HTTP头和内容。请求流将使用`HttpMessageConverters`转换为实体。请参阅 [the section called “Using HttpEntity”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-httpentity)。
- `java.util.Map` / `org.springframework.ui.Model` / `org.springframework.ui.ModelMap`用于丰富暴露于Web视图的隐式模型。
- `org.springframework.web.servlet.mvc.support.RedirectAttributes`来指定在重定向情况下使用的精确的属性集，并且还添加Flash属性（临时存储在服务器端的属性，使其可以在请求之后使用重定向）。请参见  
  [the section called “Passing Data To the Redirect Target”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-redirecting-passing-data)和[Section 18.6, “Using flash attributes”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-flash-attributes)。
- 根据`@InitBinder`方法和/或HandlerAdapter配置，命令或表单对象将请求参数绑定到bean属性（通过setter）或直接转换为字段，并进行可定制的类型转换。请参阅`RequestMappingHandlerAdapter`上的`webBindingInitializer`属性。默认情况下，这些命令对象及其验证结果将作为模型属性公开，使用命令类名称 – 例如。对于“some.package.OrderAddress”类型的命令对象的model属性“orderAddress”。 `ModelAttribute`注释可以用于方法参数来自定义所使用的模型属性名称。
- `org.springframework.validation.Errors` / `org.springframework.validation.BindingResult`验证前一个命令或表单对象的结果（即在前面的方法参数）。
- 用于将表单处理标记为完整的`org.springframework.web.bind.support.SessionStatus`状态句柄，它触发在处理程序类型级别上由`@SessionAttributes`注释指示的会话属性的清除。
- `org.springframework.web.util.UriComponentsBuilder`用于准备与当前请求的主机，端口，方案，上下文路径以及servlet映射的文字部分相关的URL的构建器。

The`Errors`or`BindingResult`parameters have to follow the model object that is being bound immediately as the method signature might have more than one model object and Spring will create a separate`BindingResult`instance for each of them so the following sample won’t work:

`Errors`或`BindingResult`参数必须遵循正在绑定的模型对象，因为方法签名可能有多个模型对象，Spring将为每个模型对象创建一个单独的`BindingResult`实例，因此以下示例将不起作用：

**BindingResult和@ModelAttribute的排序无效。**

```java
@PostMapping
public String processSubmit(@ModelAttribute("pet") Pet pet, Model model, BindingResult result) { ... }
```

注意，`Pet`和`BindingResult`之间有一个`Model`参数。 要使其工作，您必须重新排序参数如下：

```java
@PostMapping
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result, Model model) { ... }
```

| ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/note.png) |
| :--------------------------------------- |
| 支持JDK 1.8的`java.util.Optional`作为方法参数类型，其注解具有所需的属性（例如`@RequestParam`，`@RequestHeader`等）。 在这些情况下使用`java.util.Optional`相当于`required=false`。 |

#### 支持的方法返回类型

以下是支持的返回类型：

- 一个`ModelAndView`对象，其中模型隐含地丰富了命令对象和`@ModelAttribute`注解引用数据访问器方法的结果。
- 一个`Model`对象，其视图名称通过`RequestToViewNameTranslator`隐式确定，隐式丰富了命令对象的模型以及`@ModelAttribute`注解引用数据访问器方法的结果。
- 用于暴露模型的`Map`对象，其视图名称通过`RequestToViewNameTranslator`隐式确定，隐式丰富了命令对象的模型以及`@ModelAttribute`注解引用数据访问器方法的结果。
- 一个`View`对象，其模型通过命令对象和`@ModelAttribute`注解引用数据访问器方法隐式确定。处理程序方法也可以通过声明一个`Model`参数（见上文）以编程方式丰富模型。
- 解释为逻辑视图名称的`String`值，模型通过命令对象和`@ModelAttribute`注解引用数据访问器方法隐式确定。处理程序方法也可以通过声明一个`Model`参数（见上文）以编程方式丰富模型。
- 如果方法处理响应本身（通过直接写入响应内容，为此目的声明一个类型为`ServletResponse` / `HttpServletResponse`的参数），或者如果视图名称通过`RequestToViewNameTranslator`隐式确定（不在处理程序方法签名）。
- 如果该方法用`@ResponseBody`注解，则返回类型将写入响应HTTP主体。返回值将使用`HttpMessageConverters`转换为声明的方法参数类型。请参阅 [the section called “Mapping the response body with the @ResponseBody annotation”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-responsebody)。
- 一个`HttpEntity`或`ResponseEntity`对象来提供对Servlet响应HTTP头和内容的访问。实体将使用`HttpMessageConverters`转换为响应流。请参阅[the section called “Using HttpEntity”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-httpentity).
- 一个`HttpHeaders`对象返回没有正文的响应。
- 当应用程序想要在由Spring MVC管理的线程中异步生成返回值时，可以返回`Callable`。
- 当应用程序想从自己选择​​的线程生成返回值时，可以返回`DeferredResult`。
- 当应用程序想要从线程池提交中产生值时，可以返回`ListenableFuture`或`CompletableFuture`/ `CompletionStage` 。
- 可以返回`ResponseBodyEmitter`以异步地将多个对象写入响应;也支持作为`ResponseEntity`内的主体。
- 可以返回`SseEmitter`以将异步的Server-Sent事件写入响应;也支持作为`ResponseEntity`内的主体。
- 可以返回`StreamingResponseBody`以异步写入响应`OutputStream`;也支持作为`ResponseEntity`内的主体
- 任何其他返回类型都被认为是要暴露给视图的单一模型属性，使用在方法级别（或基于返回类型类名称的默认属性名称）中通过`@ModelAttribute`指定的属性名称。该模型隐含地丰富了命令对象和`@ModelAttribute`注解引用数据访问器方法的结果。

#### 通过@RequestParam绑定请求参数到方法

使用`@RequestParam`注解将请求参数绑定到控制器中的方法参数。

默认情况下，使用此注解的参数是必需的，但您可以通过将`@RequestParam`的必需属性设置为`false`（例如`@RequestParam(name="id", required=false)`）来指定参数是可选的。

#### 使用@RequestBody注解映射请求体

`@RequestBody`方法参数注解表示方法参数应绑定到HTTP请求体的值。 例如：

```java
 @PostMapping("/message")
    public void handleMessage(@RequestBody String body , Writer writer) throws IOException {
        writer.write(body);
        writer.flush();
    }

```

通过使用`HttpMessageConverter`将请求体转换为method参数。 `HttpMessageConverter`负责将HTTP请求消息转换为对象，并从对象转换为HTTP响应体。 RequestMappingHandlerAdapter支持带有以下默认`HttpMessageConverters`的`@RequestBody`注解：

- `ByteArrayHttpMessageConverter`转换字节数组。
- `StringHttpMessageConverter`转换字符串。
- `FormHttpMessageConverter`将表单数据转换为MultiValueMap &lt;String，String&gt;。
- `SourceHttpMessageConverter`converts to/from a javax.xml.transform.Source.

有关这些转换器的更多信息，请参[Message Converters](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/remoting.html#rest-message-conversion)。 另请注意，如果使用MVC命名空间或MVC Java配置，默认情况下会注册更广泛的消息转换器。 有关详细信息，请参见[Section18.16.1, “Enabling the MVC Java Config or the MVC XML Namespace”](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-config-enable)。

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <util:list id="beanList">
            <ref bean="stringHttpMessageConverter"/>
            <ref bean="marshallingHttpMessageConverter"/>
        </util:list>
    </property>
</bean>

<bean id="stringHttpMessageConverter"
        class="org.springframework.http.converter.StringHttpMessageConverter"/>

<bean id="marshallingHttpMessageConverter"
        class="org.springframework.http.converter.xml.MarshallingHttpMessageConverter">
    <property name="marshaller" ref="castorMarshaller"/>
    <property name="unmarshaller" ref="castorMarshaller"/>
</bean>
```

`@RequestBody`方法参数可以用`@Valid`注解，在这种情况下，它将使用配置的`Validator`实例进行验证。 当使用MVC命名空间或MVC Java配置时，会自动配置一个JSR-303验证器，假设在类路径上可用JSR-303实现。

就像`@ModelAttribute`参数一样，可以使用一个`Errors`参数来检查错误。 如果未声明此类参数，则将引发`MethodArgumentNotValidException`异常。 该异常在`DefaultHandlerExceptionResolver`中处理，它将向客户端发送一个`400`错误。

> 有关通过MVC命名空间或MVC Java配置配置消息转换器和验证器的信息，请参见[Section18.16.1, “Enabling the MVC Java Config or the MVC XML Namespace”](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-config-enable)。

#### 使用@ResponseBody注解映射响应体

`@ResponseBody`注解类似于`@RequestBody`。 这个注解可以放在一个方法上，并且指示返回类型应该直接写入HTTP响应体（而不是放置在模型中，或者解释为视图名称）。 例如：

```java
@GetMapping("/something")
@ResponseBody
public String helloWorld() {
    return "Hello World";
}
```

上面的例子将会导致文本`Hello World`被写入到HTTP响应流中。

与`@RequestBody`一样，Spring通过使用`HttpMessageConverter`将返回的对象转换为响应正文。 有关这些转换器的更多信息，请参阅上一节和[Message Converters](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/remoting.html#rest-message-conversion)。

#### 使用@RestController注解创建REST控制器

控制器实现REST API是一种非常普通的用例，因此只能提供JSON，XML或自定义的MediaType内容。 为了方便起见，您可以使用`@RestController`注解您的控制器类，而不是使用`@ResponseBody`注解所有的`@RequestMapping`方法。

[`@RestController`](#)是一个结合了`@ResponseBody`和 `@Controller`的构造型注解。 更重要的是，它给你的控制器带来了更多的意义，也可能在框架的未来版本中带来额外的语义。

与常规`@Controller`s一样，`@RestController`可以由`@ControllerAdvice`或`@RestControllerAdvice` bean来协助。 有关详细信息，请参阅[the section called “Advising controllers with @ControllerAdvice and @RestControllerAdvice”](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-controller-advice)部分。

```java
@RestController(value = "/music")
public class MusicResource {
    Logger log = LoggerFactory.getLogger(MusicResource.class);

    private Map<String,Music> musicMap = new ConcurrentHashMap(){
        {
            Music music = new Music();
            music.setId(1);
            music.setSonger("b1");
            music.setSongName("seve");
            music.setUrl("http://localhost:8080/music/song/seve.mp3");
            put("1",music);
        }
    };

    @RequestMapping("song")
    public String getMusic(@RequestParam("id") String song) throws FileNotFoundException {
        Music music = musicMap.get(song);
        if (music == null)
            throw new FileNotFoundException("Music not find");
        return music.toJson().toString();
    }
}
```

#### 使用HttpEntity

这`HttpEntity`是相似的`@RequestBody`和`@ResponseBody`。除了访问请求和响应主体之外`HttpEntity`（和响应特定子类`ResponseEntity`）还允许访问请求和响应头，如下所示：

```java

    @GetMapping("entity")
    public ResponseEntity<Object> handle(HttpEntity<byte[]> requestEntity){
        final StringBuffer buffer = new StringBuffer();
        buffer.append("<html><head><meta charset='utf-8'/><title>Request Headers</title></head>");
        buffer.append("<body><table>");
        HttpHeaders headers = requestEntity.getHeaders();
        headers.toSingleValueMap().forEach((k,v)->{
            String string = String.format("<tr><td>%s</td><td>%s</td></tr>",k,v);
            buffer.append(string);
        });
        buffer.append("</table></body></html>");
        HttpHeaders responseHeader = new HttpHeaders();
        responseHeader.set("Content-Type","text/html;charset=utf-8");
        responseHeader.setAccessControlAllowOrigin("*");
        return new ResponseEntity<>(buffer.toString(),responseHeader, HttpStatus.CREATED);
    }
```

#### 在方法上使用@ModelAttribute

> 1.p 到url之前，所有@modelAttribute注释的方法会全部调用
>
> 2.如果有参数定义，如果没有输入参数，则报错
>
> 

该`@ModelAttribute`注解可以对方法或方法的参数来使用。本节将介绍其在方法上的用法，下一节将介绍其在方法参数上的用法。

方法上的`@ModelAttribute`指示该方法的目的是添加一个或多个模型属性。 这些方法支持与`@RequestMapping`方法相同的参数类型，但不能直接映射到请求。 **相反，控制器中的`@ModelAttribute`方法在相同控制器内的`@RequestMapping`方法之前被调用。** 几个例子：

```java
//添加一个属性
//该方法的返回值被添加到名为“account”的模型中
//您可以通过@ModelAttribute（“myAccount”）

@ModelAttribute
public Account addAccount(@RequestParam String number) {
    return accountManager.findAccount(number);
}

// Add multiple attributes

@ModelAttribute
public void populateModel(@RequestParam String number, Model model) {
    model.addAttribute(accountManager.findAccount(number));
    // add more ...
}
```

`@ModelAttribute`方法用于填充具有常用属性的模型，例如使用状态或宠物类型填充下拉列表，或者检索诸如Account的命令对象，以便使用它来表示HTML表单上的数据。后一种情况在下一节进一步讨论。

注意两种风格的`@ModelAttribute`方法。在第一个方法中，该方法通过返回它隐式地添加一个属性。在第二个方法中，该方法接受`Model`并添加任意数量的模型属性。您可以根据需要选择两种风格。

**控制器可以有多种`@ModelAttribute`方法。所有这些方法都`@RequestMapping`在相同控制器的方法之前被调用。**

`@ModelAttribute`方法也可以在一个`@ControllerAdvice`注释类中定义，并且这种方法适用于许多控制器。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice建议控制器”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-controller-advice)一节。

#### 在方法参数上使用@ModelAttribute

如上一节所述`@ModelAttribute`，可以在方法或方法参数上使用。本节介绍了其在方法参数中的用法。

一个`@ModelAttribute`上的方法参数指示参数应该从模型中检索。如果模型中不存在，参数首先被实例化，然后添加到模型中。一旦出现在模型中，参数的字段应该从具有匹配名称的所有请求参数中填充。这被称为Spring MVC中的数据绑定，这是一种非常有用的机制，可以节省您逐个解析每个表单字段。

```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute Pet pet) { }
```

鉴于上述例子，Pet实例可以从哪里来？有几个选择：

- 由于使用`@SessionAttributes`- 可能已经在模型中- 请参阅[“使用@SessionAttributes将模型属性存储在请求之间的HTTP会话中”一节](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-sessionattrib)。
- 由于`@ModelAttribute`在同一控制器中的方法，它可能已经在模型中 – 如上一节所述。
- 它可以基于URI模板变量和类型转换器（下面更详细地解释）来检索。
- 它可以使用其默认构造函数实例化。

下一步是数据绑定。该`WebDataBinder`级比赛要求参数名称-包括查询字符串参数和表单域-以模拟通过名称属性字段。在必要时已经应用了类型转换（从字符串到目标字段类型）之后填充匹配字段。

由于数据绑定，可能会出现错误，例如缺少必填字段或类型转换错误。要检查这些错误

```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    if (result.hasErrors()) {
        return "petForm";
    }
}
```

使用一个`BindingResult`你可以检查是否发现错误，在这种情况下，渲染相同的形式通常是在Spring的`<errors>`表单标签的帮助下显示错误的。

请注意，在某些情况下，在没有数据绑定的情况下获取模型中的属性可能是有用的。对于这种情况，您可以将其注入`Model`控制器，或者使用注释上的`binding`标志：

> `ModelAttribute(binding=false) `

除了数据绑定之外，您还可以使用自己的自定义验证器调用验证，传递与`BindingResult`用于记录数据绑定错误相同的验证器。这允许在一个地方累积数据绑定和验证错误，并随后向用户报告：

```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    new PetValidator().validate(pet, result);
    if (result.hasErrors()) {
        return "petForm";
    }

    // ...
}
```

或者您可以通过添加JSR-303`@Valid`注解自动调用验证：

有关如何配置和使用验证的详细信息，请参见[第5.8节“Spring验证”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/validation.html#validation-beanvalidation)和[第5章验证，数据绑定和类型](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/validation.html)转换。

#### 使用@SessionAttributes将模型属性存储在请求之间的HTTP会话中

类型级`@SessionAttributes`注释声明特定处理程序使用的会话属性。这通常将列出模型属性或模型属性的类型，这些模型属性或类型应该透明地存储在会话或某些会话存储中，作为后续请求之间的格式支持bean。

以下代码片段显示了此注解的用法，指定了模型属性名称：

```java
@Controller
@RequestMapping("/editPet.do")
@SessionAttributes("pet")
public class EditPetForm {
    // ...
}
```

#### 使用@SessionAttribute访问预先存在的全局会话属性

如果您需要访问全局管理的预先存在的会话属性，即控制器外部（例如，通过过滤器），并且可能存在或可能不存在，`@SessionAttribute`则会使用方法参数上的注解：

```java
@RequestMapping("/")
public String handle(@SessionAttribute User user) {
    // ...
}
```

对于需要添加或删除会话属性的用例，请考虑注入`org.springframework.web.context.request.WebRequest`或`javax.servlet.http.HttpSession`控制方法。

为了在会话中临时存储模型属性作为控制器工作流的一部分，请考虑使用[“使用@SessionAttributes将模型属性存储在请求之间的HTTP会话中”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-sessionattrib)`SessionAttributes`中[所述的一节](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-sessionattrib)。

#### 使用@RequestAttribute来访问请求属性

到类似`@SessionAttribute`的`@RequestAttribute`注解可以被用于访问由滤波器或拦截器创建的预先存在的请求属性：

```java
@RequestMapping("/")
public String handle(@RequestAttribute Client client) {
    // ...
}
```

#### 使用“application / x-www-form-urlencoded”数据

以前的章节介绍了`@ModelAttribute`如何支持浏览器客户端的表单提交请求。建议与非浏览器客户端的请求一起使用相同的注解。然而，在使用HTTP PUT请求时，有一个显着的区别。浏览器可以通过HTTP GET或HTTP POST提交表单数据。非浏览器客户端也可以通过HTTP PUT提交表单。这提出了一个挑战，因为Servlet规范要求`ServletRequest.getParameter*()`一系列方法仅支持HTTP POST的表单域访问，而不支持HTTP PUT。

为了支持HTTP PUT和PATCH请求，该`spring-web`模块提供了`HttpPutFormContentFilter`可以在以下配置中的过滤器`web.xml`：

```java
<filter>
    <filter-name>httpPutFormFilter</filter-name>
    <filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>httpPutFormFilter</filter-name>
    <servlet-name>dispatcherServlet</servlet-name>
</filter-mapping>

<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
```

上述过滤器拦截具有内容类型的HTTP PUT和PATCH请求`application/x-www-form-urlencoded`，从请求的正文 中读取表单数据，并包装`ServletRequest`以便通过`ServletRequest.getParameter*()`一系列方法使表单数据可用 。

| ![](https://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/images/note.png) |
| :--------------------------------------- |
| 由于`HttpPutFormContentFilter`消耗了请求的正文，因此不应配置为依赖其他转换器的PUT或PATCH URL `application/x-www-form-urlencoded`。这包括`@RequestBody MultiValueMap`and`HttpEntity>`。 |

#### 使用@CookieValue注解映射Cookie值

该`@CookieValue`注解允许将方法参数绑定到HTTP cookie的值。

让我们考虑以下cookie已被接收到http请求：

```java
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
```

以下代码示例演示如何获取`JSESSIONID`cookie 的值：

```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie) {
    //...
}
```

如果目标方法参数类型不是，则会自动应用类型转换`String`。请参阅[“方法参数和类型转换”一节](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-typeconversion)。

#### 使用@RequestHeader注解映射请求标头属性

该`@RequestHeader`注解允许将一个方法参数绑定到请求头。

| Upgrade-Insecure-Requests | 1                                        |
| ------------------------- | ---------------------------------------- |
| Accept                    | text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8 |
| Connection                | keep-alive                               |
| Accept-Encoding           | gzip, deflate, sdch, br                  |
| User-Agent                | Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36 |
| Accept-Language           | zh-CN,zh;q=0.8                           |
| Cookie                    | __guid=111872281.1736776924917364000.1514451966899.4192; JSESSIONID=tip5tut9758i1d18521t5b9oi; monitor_count=27 |
| Host                      | localhost:8080                           |

```java

    @GetMapping("charset")
    public String requestCharset(@RequestHeader("Accept-Charset") String charset){
        return charset;
    }

```

在`Map`，`MultiValueMap`或`HttpHeaders`参数上使用`@RequestHeader`注解时，映射将填充所有标题值

如果方法参数不是`String`，则会自动应用类型转换。 

#### 方法参数和类型转换

从请求中提取的基于字符串的值（包括请求参数，路径变量，请求头和cookie值）可能需要转换为方法参数或字段的目标类型（例如，将请求参数绑定到参数中的字段`@ModelAttribute`）他们一定会。如果目标类型不是`String`，Spring将自动转换为相应的类型。支持所有简单的类型，如int，long，Date等。您可以进一步自定义通过转换过程`WebDataBinder`（见[称为“定制WebDataBinder初始化”一节](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-webdatabinder)），或者通过注册`Formatters`与`FormattingConversionService`（参见[5.6节，“春字段格式”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/validation.html#format)）

#### 自定义WebDataBinder初始化

要通过Spring定制与PropertyEditor的请求参数绑定`WebDataBinder`，可以使用`@InitBinder`控制器中的-annotated`@InitBinder`方法，`@ControllerAdvice`类中的方法或提供自定义`WebBindingInitializer`。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice建议控制器”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-controller-advice)一节。

##### 使用@InitBinder自定义数据绑定

注解控制器方法，`@InitBinder`允许您直接在控制器类中配置Web数据绑定。`@InitBinder`识别用于初始化`WebDataBinder`将用于填充命名和表示注解处理程序方法的对象参数的方法。

这种init-binder方法支持方法支持的所有参数`@RequestMapping`，除了命令/表单对象和相应的验证结果对象。Init-binder方法不能有返回值。因此，它们通常被声明为`void`。典型的参数包括`WebDataBinder`与`WebRequest`或者`java.util.Locale`允许代码注册上下文相关的编辑器。

以下示例演示如何使用`@InitBinder`为所有`java.util.Date`表单属性配置`CustomDateEditor`。

```java
@Controller
public class MyFormController {

    @InitBinder
    protected void initBinder(WebDataBinder binder) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        dateFormat.setLenient(false);
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
    }

    // ...
}
```

或者，从Spring 4.2起，考虑使用`addCustomFormatter`来指定`Formatter`实现而不是`PropertyEditor`实例。如果您碰巧`Formatter`在共享`FormattingConversionService`中安装一个基于安装程序的 方法，那么特别有用的方法可以重用于控制器特定的绑定规则调整。

```java
@Controller
public class MyFormController {

    @InitBinder
    protected void initBinder(WebDataBinder binder) {
        binder.addCustomFormatter(new DateFormatter("yyyy-MM-dd"));
    }

    // ...
}
```

##### 配置自定义WebBindingInitializer

要外部化数据绑定初始化，您可以提供`WebBindingInitializer`接口的自定义实现，然后通过为其提供自定义bean配置来启用`RequestMappingHandlerAdapter`，从而覆盖默认配置。

PetClinic应用程序中的以下示例显示了使用该接口的自定义实现的`WebBindingInitializer`配置`org.springframework.samples.petclinic.web.ClinicBindingInitializer`，它配置了几个PetClinic控制器所需的PropertyEditor。

```java
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="cacheSeconds" value="0"/>
    <property name="webBindingInitializer">
        <bean class="org.springframework.samples.petclinic.web.ClinicBindingInitializer"/>
    </property>
</bean>
```

`@InitBinder`方法也可以在`@ControllerAdvice`注解类中定义，在这种情况下，它们适用于匹配的控制器。 这提供了使用`WebBindingInitializer`的替代方法。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice建议控制器”](http://docs.spring.io/spring/docs/5.0.0.M5/spring-framework-reference/html/mvc.html#mvc-ann-controller-advice)一节。

#### 通过@ControllerAdvice和@RestControllerAdvice为控制器提供建议

`@ControllerAdvice`注解是一个组件注解允许实现类自动检测通过类路径扫描。当使用MVC命名空间或MVC Java配置时，它将自动启用。

使用`@ControllerAdvice`注解的类可以包含`@ExceptionHandler`，`@InitBinder`和`@ModelAttribute`注解的方法，这些方法将适用于`@RequestMapping`所有控制器的层次结构的方法，而不是内声明它们控制器层次。

`@RestControllerAdvice`是`@ExceptionHandler`方法默认使用`@ResponseBody`语义的替代方案。

`@ControllerAdvice`和`@RestControllerAdvice`都可以定位控制器的一个子集：

`@ControllerAdvice`注解是一个组件注解允许实现类自动检测通过类路径扫描。当使用MVC命名空间或MVC Java配置时，它将自动启用。

使用`@ControllerAdvice`注解的类可以包含`@ExceptionHandler`，`@InitBinder`和`@ModelAttribute`注解的方法，这些方法将适用于`@RequestMapping`所有控制器的层次结构的方法，而不是内声明它们控制器层次。

`@RestControllerAdvice`是`@ExceptionHandler`方法默认使用`@ResponseBody`语义的替代方案。

`@ControllerAdvice`和`@RestControllerAdvice`都可以定位控制器的一个子集：

```java
// Target all Controllers annotated with @RestController
@ControllerAdvice(annotations = RestController.class)
public class AnnotationAdvice {}

// Target all Controllers within specific packages
@ControllerAdvice("org.example.controllers")
public class BasePackageAdvice {}

// Target all Controllers assignable to specific classes
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class})
public class AssignableTypesAdvice {}
```

查看[`@ControllerAdvice`文档](http://docs.spring.io/spring-framework/docs/5.0.0.M5/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)了解更多详细信息。

#### Jackson序列化视图支持

> 过滤、筛选一个类Bean中指定的一些字段
>
> 如：Person {name,age,address,salary,...}
>
> 我只要 name,和 age： 我就只标记name和age为 `A`,获取时，指定获取标记是`A`的字段

有时将内容过滤将被序列化到HTTP响应主体的对象有时是有用的。为了提供这样的功能，Spring MVC内置了对[Jackson的Serialization Views](http://wiki.fasterxml.com/JacksonJsonViews)进行渲染的支持。

要将它用于返回`ResponseEntity`的`@ResponseBody`控制器方法或控制器方法，只需添加`@JsonView`注解，并指定要使用的视图类或接口的类参数即可：

- 依赖

```xml
  <jackson.version>2.9.4</jackson.version>  	  
<dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>
```

```java
public class Person {

    public interface WithoutAgeView{};
    public interface withAgeView{};

    private String name;
    private Integer age;

    public Person(){}

    public Person(String name,Integer age){
        this.name = name;
        this.age = age;
    }

    @JsonView(WithoutAgeView.class)
    public String getName(){
        return name;
    }

    @JsonView(withAgeView.class)
    public Integer getAge(){
        return age;
    }

}
-----------------------------------------------------------------------------------------*Controller.java
  
     @GetMapping("person-name")
    @JsonView(Person.WithoutAgeView.class)
    public Person getPerson(){
        return new Person("s",18);
    }

    @GetMapping("person-age")
    @JsonView(Person.withAgeView.class)
    public Person getPersonWithAge(){
        return new Person("埃及水电费",88);
    }
```


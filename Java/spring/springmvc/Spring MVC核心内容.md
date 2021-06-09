## Spring MVC核心内容

------

1、Spring MVC的核心架构。
2、SSM的整合。
Spring MVC的使用，我们只负责编写处理器handler和jsp页面，前端控制器只需要进行配置即可，熟悉在web.xml中的配置，配置监听器和上下文加载器，DispatcherServlet类的使用。
Spring MVC的核心架构：一个中心三大组件。
数据库操作：新建数据库，新建查询，刷新，就可看到导入的数据库文件。
Mybatis逆向工程，需要配置文件(配置包名，表与pojo的对应，表中字段与pojo属性的一一对应)和执行main方法。
Spring MVC的配置其实就是一个核心三大组件的配置，在web.xml中配置前端处理器，在spring-mvc.xml中配置视图解析器，处理器映射器和处理器适配器。配置视图解析器其实就是配置前后缀。常见的配置一定要知道怎么配置，有什么作用？
Spring MVC是对servlet的简单封装，用来处理网络请求的，拦截网络请求的。如何接收网络请求的参数？接收到参数如何进行处理？
Spring web mvc和Struts2都属于表现层的框架,它是Spring框架的一部分,我们可以从Spring的整体结构中看得出来。
目的：项目能够跑起来，MVC三层都有，并且能够连接上数据库。
Maven：使用compile命令进行项目编译，资源文件夹并没有编译进去，为什么？
返回页面与返回json数据是不一样的。
返回视图直接返回字符串，若要直接往response中写内容而不经过视图解析器时可以使用@ResponseBody注解。
要想responseBody返回Json,必须要加入json解析的jar包。
直接在Idea中创建maven项目，然后直接配置 pom.xml文件中的包依赖，项目即可运行。
新建Maven项目，然后添加依赖即可运行。
相关依赖：Spring依赖，Spring单元测试依赖，Spring webmvc相关jar，mysql驱动包，data source 相关jar包， fastjson 格式化，mybatis依赖，mybatis/spring，servlet3.0核心包。
ssm配置文件的命名：spring.xml，spring-mvc.xml，spring-mybatis.xml，
数据库配置：jdbc.properties。
使用maven来管理项目时，需要配置web.xml，会出现classpath的配置（根据路径读取配置文件）。这里的classpath到底是指的什么路径呢？就是项目打成war包之后的WEB-INF/classes文件夹下(编译以后的classes路目录下)。
编译后的classes文件夹，tomcat即可运行。
jstl包的依赖，不添加jsp页面会出现问题。javax.servlet.jsp.jstl.core.Config。
错误信息：Artifact xxx:war exploded: Error during artifact deployment.
项目Artifacts，添加war包即可，然后在tomcat的配置中添加部署Artifacts。
maven项目编译后在classes文件夹下没有编译出mybatis的xml文件以及缺少ssh的核心配置文件。在maven项目的根目录的pom.xml文件中添加指定生成xml资源文件的目录。
JSP中文乱码问题终极解决方法：pageEncoding="utf-8"。
开启Maven的依赖自动导入功能。
Idea的启动Tomcat与Maven的compile区别：tomcat要启动整个项目，而compile仅仅只需要编辑代码。
Maven的核心配置文件：setting.xml,修改成阿里的中央仓库地址，这样依赖包的下载速度比较快。
web.xml文件：web.xml文件是用来初始化配置信息：比如servlet、servlet-mapping、filter、listener、启动加载级别等。为Servlet命名并确定servlet对应的URL,其中确定Url是依赖命名的。
确定Servlet的初始化信息，设置过滤器和监听器。
Idea：直接起动，Debug启动;停止tomcat服务器的运行。
定义响应的数据格式，告诉浏览器端按照哪种格式解析数据。produces = "application/json;charset=utf-8"。
Jsp页面
它使用JSP标签在HTML网页中插入Java代码。标签通常以<%开头，以%>结束。
taglib 指令:定义一个标签库以及其自定义标签的前缀. 使用某个标签库中的标签，相当于导包。
${ }是EL表达式。EL表达式：表示你从另外一个页面传过来的参数的值，比如你从另外一个页面传了一个name为name，value为value的参数，在本页面写${name }就会显示value。用于获取变量的值。
对jsp页面中的变量进行赋值，在jsp页面中通过EL表达式把变量的值取出来。
把变量的值提交到服务器端。
按钮，提交的是表单数据，网络请求的配置在表单上。
前端控制器+业务处理器。
前端控制器：1）负责拦截网络请求，并且把网络请求分发到对应的控制器，不负责具体的业务逻辑；2）把结果数据转发给jsp页面
业务处理器：1）负责具体的业务逻辑处理。。
Handler其实就是Controller层。
Spring MVC的架构流程
Spring MVC架构流程（Spring MVC一个核心三大组件）
1、用户发送请求至前端控制器DispatcherServlet;
2、DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3、处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4、DispatcherServlet通过HandlerAdapter处理器适配器调用处理器。
5、执行处理器(Controller，也叫后端控制器)。
6、Controller执行完成返回ModelAndView。
7、HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
9、ViewReslover解析后返回具体View。
10、DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11、DispatcherServlet响应用户。
请求查询处理器，请求处理器执行具体的业务逻辑。
整合springMVC和Mybatis框架
整合目标：控制层采用SpringMVC，持久层使用myBatis实现。
如何进行整合?（Jar包+配置文件,Spring的配置文件，MyBatis的配置文件，数据库文件，如何读取加载配置文件？）
新建web动态工程？
Spring监听器：当Spring启动的时候，加载配置文件。
三大框架SSM的整合都是这个思路，把这个整合过程记下来，原理是什么。
1) 如何读取Spring的配置文件？ 关乎项目结构。
2) 如何读取Spring MVC的配置文件？
3) 在什么时候加载MyBatis的配置文件？
整合思路：
一、Dao层：
1、SqlMapConfig.xml，空文件即可，但是需要文件头。
2、applicationContext.xml; Spring配置文件 a)数据库连接池b)SqlSessionFactory对象，需要spring和mybatis整合包下的。c)配置mapper文件扫描器。
二、Service层：
1、applicationContext-service.xml包扫描器，扫描@service注解的类；
2、applicationContext-trans.xml配置事务；
三、Controller层：
1、Spring MVC.xml  a)包扫描器，扫描@Controller注解的类。b)配置注解驱动。c)配置视图解析器
四：Web.xml文件：
1、加载Spring配置文件  2、配置前端控制器。
注解式事务
Spring中如何开启事务？



@ResponseBody注解用于将Controller的方法返回的对象，通过springmvc提供的HttpMessageConverter接口转换为指定格式的数据如：json,xml等，通过Response响应给客户端。

@RequestMapping此注解主要用于映射路径(处理器映射器)。通过@RequestMapping注解可以定义不同的处理器映射规则。
1）URL路径映射：value的值是数组，可以将多个url映射到同一个方法。
2）添加在类上面，对url进行分类管理。限制此类下的所有方法请求url必须以请求前缀开头。
3）请求方法限定：u限定GET方法@RequestMapping(method = RequestMethod.GET)
如果通过POST访问则报错：HTTP Status 405 - Request method 'POST' not supported。
通过数组方式实现：GET和POST都可以  @RequestMapping(method = {RequestMethod.GET,RequestMethod.POST})

Controller 方法返回值 ：
1、返回ModelAndView：controller方法中定义ModelAndView对象并返回，对象中可添加model数据、指定view。
2、返回void，在Controller方法形参上可以定义request和response，使用request或response指定响应结果：
1）使用request转发页面，如下：
request.getRequestDispatcher("页面路径").forward(request, response);
request.getRequestDispatcher("/WEB-INF/jsp/success.jsp").forward(request,response);
2）可以通过response页面重定向：
esponse.sendRedirect("url")response.sendRedirect("/springmvc-web2/itemEdit.action");
3）可以通过response指定响应结果，例如响应json数据如下：
response.getWriter().print("{\"abc\":123}");
3、 返回字符串（便于解耦，视图与数据分离） 把结果对象转化成json 字符串并进行返回。
1）controller方法返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址。
2）Redirect重定向。3）forward转发。

```jade
/**
 * 测试请求转发与重定向
 * 用request进行转发
 */
@RequestMapping(value = "/requestDis.do", method = RequestMethod.GET)
public String getResultData(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //请求进来以后，直接转发到请求结果页面(只能使用这种方式)
    //1)url没有变化，2)只是一个请求，服务器内部进行请求转发。
    //request.getRequestDispatcher("/WEB-INF/jsp/success.jsp").forward(request, response);
 
    //2)重定向到指定页面，两个请求，会有两个request对象,地址栏中URl会修改。
    //重定向到某一个接口请求；如何重定向到某一个jsp页面
    //response.sendRedirect("/home/index.do");
    //通过ModelAndView进行实现（进行页面跳转）
 
    //这句话的意思，是让浏览器用utf8来解析返回的数据
    //response.setHeader("Content-type", "text/html;charset=UTF-8");
    //这句话的意思，是告诉servlet用UTF-8转码（编码），而不是用默认的ISO8859
    //response.setCharacterEncoding("UTF-8");
    //response.getWriter().write("测试数据");
 
    //指定逻辑视图名，经过视图解析器解析为jsp物理路径
    //return "itemList";
 
    //Redirect重定向
    //重定向后浏览器地址栏变更为重定向的地址，
    //重定向相当于执行了新的request和response，所以之前的请求参数都会丢失.
    //如果要指定请求参数，需要在重定向的url后面添加 ?itemId=1 这样的请求参数.
    //return "redirect:/product/getAll.do?id=1";
 
    //请求转发到指定的jsp页面，特别常用
    return "forward:/product/getAll.do?id=1";
}
```
spring mvc 数据绑定

```java
/**
 * 绑定数组类型
 * 在商品列表页面选中多个商品，然后删除。
 * 功能要求商品列表页面中的每个商品前有一个checkbok，
 * 选中多个商品后点击删除按钮把商品id传递给Controller，
 * 根据商品id删除商品信息。提交两个商品的id即可。
 *
 * @param ids
 * @param response
 * @throws IOException
 */
//在jsp页面与java后台的交互还是不熟悉，一定要精通。对表单的网络请求不熟悉。
//Controller方法中可以用String[]接收，或者pojo的String[]属性接收。
//两种方式任选其一即可。  (数组类型和pojo的包装类型)
@RequestMapping(value = "/delet.do", method = RequestMethod.POST)
public void deleteProductById(Integer[] ids, HttpServletResponse response) throws IOException {
    response.sendRedirect("/home/index.do");
}
 
//绑定pojo包装类型
@RequestMapping(value = "/delete.do", method = RequestMethod.POST)
public void deleteProductByIds(QueryVo queryVo, Integer[] ids, HttpServletResponse response) throws IOException {
    for (Integer id : ids) {
        //把数据打印到控制台
        System.out.println(id);
    }
    response.sendRedirect("/home/index.do");
}
 
//绑定pojo类型
@ResponseBody
@RequestMapping(value = "/updatePro.do", method = RequestMethod.POST,produces = "application/json;charset=utf-8")
public String deleteAllProduct(Items item) {
    System.out.println(item.toString());
    return "测试成功";
}
```
spring mvc参数绑定：数组类型的参数绑定，List类型的绑定
@RequestMapping注解的使用  详细使用
Controller方法返回值：ModelAndView，void，String。
spring mvc中异常处理。
spring mvc实现图片上传处理，如何将一个文件绑定到形参上。
spring mvc中Json数据交互（移动端访问后台接口）
spring mvc中使用RESTful编程风格
spring mvc中的拦截器
用户请求到前端控制器、让处理器映射器去找相应的路径，对应的方法。
————————————————
版权声明：本文为CSDN博主「陈如水」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/chenrushui/article/details/80613964
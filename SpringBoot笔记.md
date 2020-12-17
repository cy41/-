## SpringBoot笔记

### 一、注解部分

#### 一、@RequestMapping("path")

通过访问path调用方法。

#### 二、@ResponseBody

可以直接将参数返回给浏览器界面输出，一般有json和视图形式。

#### 三、@PostMapping("path")

用于接受path的post请求。

#### 四、@GetMapping("path")

用于接收浏览器发出的一个get请求，一般需要在一个返回String的方法上书写。

访问path地址，然后发出一个请求指向String，去访问，可以实现地址跳转。

例：访问upload/page 之后，发送一个get请求访问upload。

```java
	@GetMapping("/upload/page")
    public String uploadPage() {
        return "upload";
    }

```



#### 五、@RequestParam("name")

用于匹配参数，可接受一个表单中所有name一致的部分，一般在方法形参前使用。

例：

```java
public Map<String, Object> upload1(@RequestParam("photo") MultipartFile[] photos)     {  
    .......
} 
```

#### 六、 @Autowired 

自动导入，相当于new。他会根据属性的类型找到对应的Bean注入。

例：

```java
public class CY{
    @Autowired
    private Fuck lt; //相当于Fuck lt=new Fuck();
}
```

###### 消除歧义性 @Primary 和 @Quelifier

比如有一个动物接口，有两个他的实现类，分别为cat和dog，那么当我们在service类中使用Autowired 装配Animal类时就会报错，这是因为有两个实现类，Ioc容器不知道你到底注入的是哪个类，故这时产生歧义性。

@Primary表示一个修改优先权的注解，优先装配该实现类。

例：

```java
@Component //
@Primary //这里的注解告诉Ioc容器当有多个同类型的Bean时，优先使用该类注入。
public class Cat implements Animal{
    
}
```



@Quelifier("name") 表示按照名称找到对应的Bean注入。

```java
@Autowired
@Qualifier("dog") //与Autowired同时使用即形成 按照类型装配，按照名称装配，两者结合同时装配。
private Animal animal ;
```

这个还可以在构造方法中使用，比如：

```java
@Component
public class Cy{
    Animal animal;
    public Cy(@Autowired @Qualifier("dog") Animal animal){
        this.animal=animal;
    }
}

```





#### 七、浏览器输入地址，后台跳转到jsp表单页面，表单提交，后台捕获数据

```java
@GetMapping("/path1")
public String hello(){
    return "/hello";
}
```

浏览器通过地址path1发出一个Get请求，后台跳转到/hello.jsp下。

```jsp
	<form action="./path2" method="post">
        <input type="text" name="hello">
        <input type="submit">
    </form>
```

jsp表单向path2路径发出post请求。

```java
@PostMapping("/path2")
public String Put(RequestParam("hello") String name){
    return name;
}
```

之后地址栏跳转到path2，name捕获表单中name为"hello"的信息，然后输出。

#### 八、一个简单的MVC过程

1.首先要访问某个jsp页面， 你需要在Controller类中通过GetMapping的方式与你要访问的jsp页面进行映射。

例：

```java
    @GetMapping("/select")
    public String ask(){
        return "/file/select";
    }
```



2.浏览器输入路径访问，页面在jsp表单处等待输入信息，接下来输入测试数据，表单提交为post请求，故同样的需要在Controller类中进行映射接受post请求：

```java
jsp页面;
<body>
    <form action="HHH" method="post">
        <input name="id" type="text" />
        <input type="submit"/>
    </form>
</body>
```

```java
    
	@PostMapping("/HHH")
    public ModelAndView mav(@RequestParam("id") String id,ModelAndView mv){
        System.out.print(id);
        User user=us.findById(id);
        mv.addObject("user",user);
        mv.setViewName("/file/showUser");
        return mv;
    }
```

3.控制层接收到数据，通过服务层进行操作，服务层又向更底层的Dao层进行操作。

4.视图创建，将查询结果放入视图中。

5.跳转到jsp等视图界面进行视图展示。

#### 九、@ModelAttribute

通常用在Controller的某个方法上，只要某个路径在这个Controller下，都会优先执行完所有的标注有ModelAttribute的方法，再执行对应路径的方法。

可以理解为Controller类的一些初始化。

#### 十、@PathVariable

可以将URL中的值映射到方法参数中。

例：

```java
@RequestMapping(value="/get/{id}.json")
@ResponseBody
public User getById(@PathVariable("id") Long id){
    return userService.getUserById(id);
}
```

#### 十一、@RequestPart

用于文件上传。

#### 十二、@Bean

将组件注册在容器中，引起springboot注意。

###### Bean的作用域

默认是单例的，也即若多个变量指向实例，则指向一个实例，例：

```java
@Component //标注组件
public class ScopeBean{
    
}

public class IocTest{
    public static void main(String[] args){
        AnnotationConfigApplicationContext ctx=new AnnotationConfigApplicationContext(AppConfig.class);
        ScopeBean sc1=ctx.getBean(ScopeBean.class);
        ScopeBean sc2=ctx.getBean(ScopeBean.class);
        System.out.println(sc1==sc2);//会输出true;
    }
}
```

修改作用域可以加一个注解：

```java
@Scope("ConfigurableBeanFactory.SCOPE_PROTOTYPE") 
//作用域为原型，也即每次都会新建一个Bean对象;
单例为：SCOPE_SINGLETON;
```

需要注意的是ConfigurableBeanFactory只能提供单例与原型两种作用域，

还可以使用WebApplicationContext定义别的作用域如：

1. 请求SCOPE_REQUEST
2. 会话SCOPE_SESSION
3. 应用SCOPE_APPLICATION



#### 十三、WebMvcConfigurer

用来全局定制化springboot的MVC特性，可以通过实现该接口来配置应用的MVC全局特性。

@Configuration标注了该类是一个Connfigurer类，用于MVC的全局配置。

```java
//拦截器
public void addInterceptors(InterceptorRegistry registry){
    
}

//跨域访问配置
public void addCorsMappings(CorsRegistry registry){
    
}

//格式化
public void addFormatters(FormatterRegistry registry){
    
}

//URL到视图的映射
public void addViewControllers(ViewControllerRegistry registry){
    registry.addViewController("/").setViewName("file/index");//例，访问http://localhost:8080/即可访问到index.jsp文件;
}

```

#### 十四、@Component

通过扫描装配Bean，当然为了让spring容器装配这个类，需要在Configurer类中加入ComponentScan注解，但是他只会扫描该类所在包以及子包中的。故可以这么写：

@ComponentScan("包名");即可。

#### 十五、@Repository

标注该类是一个数据库或其他NoSQL访问类。

#### 十六、拦截器

所有的拦截器都要实现HandIerInterceptor 接口，并且重写方法：

```java
	//处理器执行前方法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return false;
    }

    //处理器处理后方法
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    //处理器完成后方法
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
```

方法执行流程：

![image-20191122195813825](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20191122195813825.png)

简单的页面访问拦截器：

对于某些网页的访问，需要用户登录后才可以访问，这时可以对该页面添加登录拦截器，检查用户是否登录。

通常通过HttpSession来进行检查，当用户登录后就将该用户添加进session中，注销即删除该用户的session中的记录。

拦截器类：

```java
public class Mylnterceptor implements HandlerInterceptor{
    //重写方法;
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("处理器前方法");
        Object user=request.getSession().getAttribute(MyConfigurer.SESSION_KEY);
        if(user==null){
            response.sendRedirect("/login");
            return false;
        }
        return true;
    }
}
```

重写的配置类：

```java
@Configuration
public class MyConfigurer implements WebMvcConfigurer {
    public final static String SESSION_KEY = "user";
    
    //配置拦截器类;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        InterceptorRegistration mi=registry.addInterceptor(new Mylnterceptor());
        mi.addPathPatterns("/success");//添加拦截路径;

    }
}
```

控制类中的方法：

```java
@GetMapping("/login")
    public String login(){
        return "login";
    }
    @PostMapping("login")   //通过表单获取参数;
    public ModelAndView login(@RequestParam("id") String id,@RequestParam("password") String password,HttpSession session){
        User user=new User(id,password);
        ModelAndView mv=new ModelAndView();
        if(us.check(user)){
            mv.addObject("user",user);
            session.setAttribute(MyConfigurer.SESSION_KEY, user);
            mv.setViewName("/success");
        }
        else mv.setViewName("/fail");
        return mv;
    }
```



#### 十七、@Transactional

开启事务，方法运行成功则提交(将数据写入数据库表)，运行失败抛出**runtimeexception及其子类**异常则回滚。

若要指定其他异常回滚则需要添加属性：

```java
@Transactional(rollbackFor = Exception.class)
```

#### 十八、@MapperScan("packagepath")

在springboot应用上写此注解，可以扫描对应的包下的mybatis需要实现的原子操作接口。

#### 十九、mybatis简单事务逻辑

1. 原子操作定义为接口，加注解Mapper，在xml中书写数据库语句，mybatis自动实现对应类。
2. service层进行原子操作的组合
3. controller依旧
4. UI层依旧

#### 二十、使用热部署

```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
     <optional>true</optional>
 </dependency>
```

#### 二十一、@RestController

相当于Controller与Response结合

#### 二十二、@PropertySource

​	若同时存在多个properties文件，使用对应的文件去初始化类中的属性

![image-20200207221915692](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200207221915692.png)



![image-20200207221907112](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200207221907112.png)

#### 二十三、条件装配Bean

​	@Conditional，配合Condition（org.springframework.context.annotation.Condition）类

![image-20200207222916136](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200207222916136.png)

![image-20200207222930331](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200207222930331.png)

​	matches方法用于判断是否满足指定的条件，亦可用于判断当前操作系统等。

#### 二十四、@Profile

​	切换环境





### 二、一些库

#### 一、文件上传

Spring MVC提供了MultipartFile接口作为参数处理文件上传。

主要方法：

1. getOriginalFilename 获取上传的文件名字
2. getInputStream 获取一个InputStream
3. getSize 上传文件的大小
4. isEmpty 文件上传内容为空或无文件上传。

**若上传单个文件使用MultipartFile类对象接收单个文件上传，若是同时上传多个文件，则使用MultipartFile数组接收。**

#### 二、关于Model

###### 1.方法

```java
Model addAttribute(String name,Object value); //向模型添加一个变量，名字为name，值为value;
Model addAttribute(Object value);//向模型添加一个变量，名字为该类名首字母小写后的字符串;
Model addAllAttribute(Map attributes);//添加多个变量，若存在则覆盖;
Model mergeAttribute(Map attributes);//添加多个变量，若存在则跳过;
boolean containsAttribute(String name);//判断是否存在变量;
```

###### 2.简单使用

Model作为方法参数时，springMVC框架在调用方法前会自动创建Model;

浏览器发送get请求，需要传一个id，方法接受，然后将查询结果放入model中，然后跳转到jsp页面展示数据;

```java
	@GetMapping("model")
	public String useModel(Integer id,Model model) {
		User user=userService.getUser(id);
		model.addAttribute("user",user);
		return "user/details";
	}
```

#### 三、关于ModelAndView

类似Model，但额外提供了一个视图名称，即可以直接跳转到指定界面；

例：

```java
    @GetMapping("/mav")
    public ModelAndView useModelAndView(Integer id, ModelAndView mv) {
        User user = userService.getUser(id);
        // 设置数据模型
        mv.addObject("user", user);
        // 设置视图名称//需要跳转的界面;
        mv.setViewName("user/details");
        System.out.println(mv);//测试一下，输出路径;
        return mv;//相当于跳转页面，和Model中返回路径是一个原理;
    }
```

当然ModelAndView既可以通过方法声明，也可在方法中构造;





### 三、杂乱

#### 一、jsp表单提交数组

只需要将多个input的name设置一致，即可在java方法中通过匹配参数通过数组接收。

#### 二、jsp文件一般存放在：

webapp->WEB-INF-jsp文件夹下。

#### 三、Get与Post

- *GET* - 从指定的资源请求数据。
- *POST* - 向指定的资源提交要被处理的数据

#### 四、分层

Controller 控制层，控制数据流向模型对象，并在数据变化时更新视图，使视图与模型分离

Dao 数据访问层，通常为对数据进行原子操作，增删改查

Service 服务层，多种原子操作的混杂

Model 模型

View 视图，数据可视化

#### 五、超链接跳转

需将被跳转页面放在static文件夹中。

#### 六、application.properties

配置文件。

例：为jsp文件进行拼接，路径访问时只需要书写具体名称即可。

```java
spring.mvc.view.prefix=/WEB-INF/jsp/      //前缀
spring.mvc.view.suffix=.jsp               //后缀
```

#### 七、request的两个接受参数的方法

1.getAttribute("name") 必须先set才能get到。

2.getParameter("name") 从post请求中接收参数。

#### 八、使用Spring EL

例：

```java
//赋值字符串
@Value("#{'字符串'}")
private String str=null;

//科学计数法赋值
@Value("#{9.3E3}")
private double d;

//赋值浮点数
@Value("#{3.14}")
private float pi;

//从其他Bean属性获取值
@Value("#{beanName.str}")
private String otherBeanProp=null;

//?用法 先判断是否为null，若不空再执行后面的方法;
@Value("#{beanName.str?.toUpperCase()}")
private String other=null;

EL支持的运算：
//运算
#{1+2}
//字符串比较
#{beanName.str eq 'asdf1'}
//数字比较
#{100 == 100}
//字符串连接
#{beanName.str + 'asdf'}
//三目运算符
#{1000>100?'true':'false'}
private String res=null;
```

#### 九、springboot目录结构

![image-20191128095721999](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20191128095721999.png)

```java
src
    main
    	java
    		configurer//配置类;
    		controller//控制类;
    		dao//原子操作;
    		entity//实体类;
    		interceptor//拦截器;
    		service//服务类;
    		Application.java//启动类;
    	resources
    		static//静态资源;
    		templates//存放网页;
    		application.yml/.properties //配置文件;
    text//测试文件;
target
web
pom.xml //各种依赖;
    
```

#### 十、mybatis一些东西

1. 插入数据，自增id的回显，useGeneratedKeys="true" keyProperty="class.id"  keyColumn="table.id"
2. 一对多 collection  一对一 association

#### 十一、Bean的作用域

​	![image-20200207223505534](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200207223505534.png)

### 四、 AOP

​	结合约定那一部分代码实现理解。

​	![image-20200209210118310](SpringBoot%E7%AC%94%E8%AE%B0.assets/image-20200209210118310.png)




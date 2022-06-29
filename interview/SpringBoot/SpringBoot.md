[toc]









### 运行环境

- java 11
- maven 3.8.4



### 教程材料

- B站视频网站：
  - https://www.bilibili.com/video/BV19K4y1L7MT?p=1
- 笔记网站：
  - https://www.yuque.com/atguigu/springboot
- Spring官网：
  - https://spring.io/
- Spring解决方案：
  - https://spring.io/projects/spring-framework
- SpringBoot reference documentation
  - https://docs.spring.io/spring-boot/docs/current/reference/html/
  - Using SpringBoot
    - https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using
  - Developer Tools
    - https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.devtools





### 基础问题1：

如何查看容器中有哪些组件，以及这些组件的状态。

```
@SpringBootApplication
public class SpringbootLearnApplication {

    public static void main(String[] args) {
        // 返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(SpringbootLearnApplication.class, args);

        // 查看容器内的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println("容器组件: " + name);
        }

        // 从容器中获取组件
        AccountMapper accountMapper = run.getBean("accountMapper", AccountMapper.class);
    }
}

```



### 问题：

​	注解@Vaild注解的功能是什么？@NotNull，@NotBlank功能是什么

​	通常Spring接口的数据必须要经过校验才能进入接口，这些注解可以帮忙进行校验。








### 基础注解：

#### 问题1：

	Maven中的properties，build，repository中都是什么意思？
	
	Maven是不是得从头到尾学习一下？





#### 问题2：

	IoC和AOP至今也没有理解到本质，也没有办法讲清楚





#### 知识点1：文件路径和意义

bean：对象(组件)，在Spring中，每一个对象Class，都可以被称为一个组件

config：配置文件

controller：业务控制，接口一般都放在这里

service：业务逻辑

Dao：数据访问



#### 注解组件

- @Conponent  普通组件
- @Controller  控制器
- @Service  业务逻辑组件
- @Repository  数据库组件

	

#### SpringBootApplication注解

    该注解是SpringBoot启动的注解，其内部相当于实现了如下注解

```
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```





#### Component注解

	一般放在一个Class上，将Class注入到容器中







#### Configuration注解

// todo 感觉这块没有理解清楚

@Configuration注解通常用来自动做配置的类

- 轻量配置，全量配置   @Configuration(proxyBeanMethods = true)

  - proxyBeanMethods = true
    - 全量配置Full模式，每次调用容器中的组件，都是同一个
  - proxyBeanMethods = false
    - 轻量配置Lite模式，每次调用都会重新生成一个组件

  如下代码中，每次调用cat()时，都会重新实例化一个全新的组件。

```
/**
 * Configuration
 * SpringBoot中的各种配置，等同于从前用Spring中的xml文件配置
 *
 * @since 1.0.0.100
 */
@Import({AtImport.class})
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(Car.class)
public class MyConfig1 {
    /**
     * Bean注解：给容器中添加组件，以方法的名称作为组件id，返回类型就是组件的类型
     */
    @Bean
    public User user01() {
        return new User("ZhangSan", 18);
    }

    @Bean("tom") // 可以自定义组件的名称
    public Pet cat() {
        return new Pet("tomcat");
    }

    // 条件组件，当组件中有tom的情况下，才会引入下边的组件
    @ConditionalOnBean(name = "tom")
    public Pet cat2() {
        return new Pet("tomcat2");
    }
}
```





#### 知识点3：ComponentScan注解

	@ComponentScan(“com.abc.def.xxx”)注解可以修改包扫描时应该扫描的位置







#### 知识点4：Import注解

	@Import组件，可以往容器中导入组件，一般需要放在一个组件上

- 默认组件的名字就是全类名

  如下所示，在加载MyConfig1时，会加载组件AtImport，

```
@Import({AtImport.class})
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(Car.class)
public class MyConfig1 {
    /**
     * Bean注解：给容器中添加组件，以方法的名称作为组件id，返回类型就是组件的类型
     */
    @Bean
    public User user01() {
        return new User("ZhangSan", 18);
    }

    @Bean("tom") // 可以自定义组件的名称
    public Pet cat() {
        return new Pet("tomcat");
    }

    // 条件组件，当组件中有tom的情况下，才会引入下边的组件
    @ConditionalOnBean(name = "tom")
    public Pet cat2() {
        return new Pet("tomcat2");
    }
}
```

```
@Data
public class AtImport {
    private String name;
    private int age;
}
```



#### 知识点5：Conditional注解

	@Conditional注解是满足了特定的条件，才会给容器中添加组件

- ConditionalOnBean(name = "name") 当容器中的组件包含"name"时会引入
- ConditionalOnMissingBean  缺少某一个组件后，则会向容器中增加组件
- ConditionalOnResource
- ....





#### 知识点6：ImportResource注解

	@ImportResource注解会将原始的spring中写的.xml配置的组件，加入到容器中







#### 知识点7：ConfigurationProperties注解

		在某一个组件通过@Component或@Bean等注入到容器后，可以用ConfigurationProperties注解，将配置文件中的值注入到组件中。要求名字必须要一致。
	
	    通常用来实现数据库相关的组件。

```
@ConfigurationProperties(prefix = "mycar")
@Component
@Data
public class Car {
    private String brand;
    private Integer price;
}
```

	要求配置文件中的值为：

```
mycar.brand=BYD
mycar.price=1000
```

		至此，容器中的Car组件即为BYD和1000。





#### EnableConfigurationProperties注解

		一般用在@Configration注解同级，表示开启属性配置，并将组件自动配置并注入到容器中。
	
		config文件下：

```
@Configuration
@EnableConfigurationProperties(Car.class)
```

		配置文件：

```
mycar.brand=BYD
mycar.price=1000
```

		对象bean文件：	

```
@Data
@ConfigurationProperties(prefix = "mycar")
public class Car {
    private String brand;
    private Integer price;
}
```

		容器启动之后，就会有car出现在容器中。





#### AutoWired注解

		去重启中取值，容器中没有就是null





#### SpringBootConfiguration注解

		@Configuration代表当前是一个配置类





#### ImportResource注解

	有一些旧的Spring工程，仍然在使用旧的.xml配置，因此需要将其所在的位置，加载到容器中。

```
@Configuration
@ImportResource("classpath:others/beans.xml")
public class MyConfig1 {
	
}
```





### SpringBoot自动配置原理入门

// todo 这个部分理解的不行



SpringBootApplication注解由三个注解合成

- SpringBootConfiguration

- ComponentScan

- EnableAutoConfiguration 最重要的一个注解，由如下两个注解合成
  - AutoConfigurationPackage
  - Import





==============================================

视频18集及以后

==============================================

#### DevTools 

- SpringBoot做热部署需要的依赖

  ```
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
  </dependency>
  ```

引入该依赖，完成代码修改之后，点击build，就会自动完成部署，算是无缝衔接。

这部分算是重新编译，如果想要用真正的热部署，需要用付费版的JRebel



#### yaml自动装配

yaml中经常会用来配置组件，代码中不进行任何硬编码，这样修改起来会更方便。

yaml配置的格式需要学习一下，例子如下

Java类：

```
@Data
public class Person {
	private String userName;
	private Boolean boss;
	private Date birth;
	private Integer age;
	private Pet pet;
	private String[] interests;
	private List<String> animal;
	private Map<String, Object> score;
	private Set<Double> salarys;
	private Map<String, List<Pet>> allPets;
}
```

Yaml写法：

```
# yaml表示以上对象
person:
  userName: zhangsan
  boss: false
  birth: 2019/12/12 20:12:33
  age: 18
  pet: 
    name: ergou
    weight: 23.4
  interests: [篮球,游泳]
  animal: 
    - jerry
    - mario
  score:
    english: 
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: {first: 128,second: 136}
  salarys: [3999,4999.98,5999.99]
  allPets:
    sick:
      - {name: tom}
      - {name: jerry,weight: 47}
    health: [{name: mario,weight: 47}]
```





#### 配置处理器

[参考文档传送门](!https://blog.csdn.net/qq_27384769/article/details/108723371)

SpringBoot在配置yaml文件时，有时候需要配置传统的xml或者properties，此时需要依赖

```
<!-- spring配置 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

配置文件中写入

```
author.name=zhangsan
author.age=20
```

在类中写注解@PropertySource

```
@Component
@PropertySource(value = {"classpath:static/config/authorSetting.properties"},
        ignoreResourceNotFound = false, encoding = "UTF-8", name = "authorSetting.properties")
public class AuthorTest {
 
    @Value("${author.name}")
    private String name;
    @Value("${author.age}")
    private int age;
}
```





## SpringBoot Web开发

对应21视频--无意义文档



### 静态资源

#### 静态资源目录

	静态资源放置在如下路径下可以自动读取，如果存储的为文件，直接在接口中访问资源名即可直接访问。

- resources/public
- resources/static
- resources/resources
- resources/META-INF/resources



#### 静态资源访问前缀

有些时候想要让静态资源访问时增加前缀，可以在yaml中配置

```
spring:
  mvc:
    static-path-pattern: /res/**
```

原始访问：

```
http://localhost:8888/bug-public.png
```

修改后访问：

```
http://localhost:8888/res/bug-public.png
```



#### 静态资源路径配置

默认情况会有四个文件夹下可以配置，但如果想指定特定文件夹为资源文件，可以在yaml中配置

```
spring:
  web:
    resources:
      static-locations: classpath:/others
```

```
spring:
  web:
    resources:
      static-locations: [classpath:/others, classpath:/others2]
```

现在及为在resources中的others的文件夹内为资源文件，原始默认的资源文件*全部失效*







#### 静态资源配置欢迎页

- 静态资源路径下  index.html

- - 可以配置静态资源路径
  - 但是不可以配置静态资源的访问前缀。否则导致 index.html不能被默认访问

- 每次访问项目根路径的时候，就会自动跳转到欢迎页上

- 注意*不能配置资源路径*

```
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致welcome page功能失效

  resources:
    static-locations: [classpath:/haha/]
```

配置好一个index.html，直接访问接口，就会进入欢迎页

访问接口

```
http://localhost:8888/
```

index.html

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>

    <body>
        <h1>spring欢迎页</h1>
    </body>
</html>
```



#### webjar自动映射

引入三方库webjar，可以将引入的三方库依赖加载到容器中

```
<dependency>
	<groupId>org.webjars</groupId>
	<artifactId>jquery</artifactId>
	<version>3.5.1</version>
</dependency>
```

自动映射  /webjars/**

访问地址后面地址要按照依赖里面的包路径

```
http://localhost:8080/webjars/jquery/3.5.1/jquery.js 
```











#### 静态资源配置原理

	= =|||  
	
	实话实说，这块没有咋看懂，大概看了一圈



## 请求参数处理



31 视频



#### @MatrixVariable 矩阵变量 注解

矩阵变量通常用来传递多个参数，用来做多个选择

路径变量通常是使用@RequestParam注解进行获取，例如

```
/car/{path}/abc?xxx=xxx&aaa=aaa
```

MatrixVariable 可以在路径中传递一个矩阵

```
/car/path;low=34;brand=byd,audi,yd
```

这个注解通常来解决Cookie禁用的情况。

通常情况，矩阵变量是默认关闭的，如果想要开启，必须注入一个组件，如

```
//开启矩阵变量
@Configuration(proxyBeanMethods = false)
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        // 不移除后边的内容，矩阵变量可以生效
        urlPathHelper.setRemoveSemicolonContent(false);
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```

接收矩阵变量的接口

```
@RestController
public class ParameterTestController {
    //1、语法： 请求路径：/cars/sell;low=34;brand=byd,audi,yd
    //2、SpringBoot默认是禁用了矩阵变量的功能
    //      手动开启：原理。对于路径的处理。UrlPathHelper进行解析。
    //              removeSemicolonContent（移除分号内容）支持矩阵变量的
    //3、矩阵变量必须有url路径变量才能被解析
    @GetMapping("/cars/{path}")
    public Map carsSell(@MatrixVariable("low") Integer low,
                        @MatrixVariable("brand") List<String> brand,
                        @PathVariable("path") String path){
        Map<String,Object> map = new HashMap<>();
        map.put("low",low);
        map.put("brand",brand);
        map.put("path",path);
        return map;
    }
}
```

矩阵变量的前端

```
http://localhost:8888/cars/sell;low=34;brand=byd,audi,yd
```

请求结果可以解析矩阵变量，如

```
{"path":"sell","low":34,"brand":["byd","audi","yd"]}
```





### 知识：

Spring中的IOC和AOP是什么意思？

- IoC ( Inversion of Control，控制反转 )，从人控制业务改变成代码框架控制业务。
- AOP( Aspect-Oriented Programming，面向切面编程 )





### 问题

- Spring中的注解都是什么意思
  - Controller
  - RestController
  - ResponseBody
    - ResponseBody写在接口上表示可以直接使用，也可以接受其他接口的跳转，如果不写则不能接受其他接口









===================================================================





### 14集 - 17集

主要讲怎么看文档来查看组件配置，但是只能记录结论，怎么看依然没有搞明白

===================================================================



#### 文档材料：

[application-properties配置主页](!https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.core)





#### 修改启动banner

有两种方式修改启动时的banner

- 直接在resources文件夹下 放置一个banner.txt 或 banner.jpg
- 在.yaml或properties的配置中修改配置，这时banner的名字可以任意修改

```
# 配置 banner
spring.banner.location=classpath:banner/banner2.txt
```

=======================================================================









视频26集及以后

==========================================================================

Rest映射和源码解析



### Controller相关注解

- RestController注解中通常会存放需要返回值的注解
  - RestController是Controller和ResponseBody的结合版
- Controller注解通常会放跳转型的接口





### RestController注解中接口的写法

// 后边讲了很多原理，我没有听懂 todo

- @RequestMapping
- @GetMapping
- @PostMapping
- @DeleteMapping
- @PutMapping

```
package com.springboot.learn.springbootlearn.controller;

import org.springframework.web.bind.annotation.*;

@RestController
public class HelloController {

    @RequestMapping("/bug.jpg")
    public String hello() {
        return "aaa";
    }

//    @RequestMapping(value = "/user", method = RequestMethod.GET)
    @GetMapping("/user")
    public String getUser() {
        return "GET-张三";
    }

//    @RequestMapping(value = "/user", method = RequestMethod.POST)
    @PostMapping("/user")
    public String saveUser() {
        return "POST-张三";
    }

//    @RequestMapping(value = "/user", method = RequestMethod.PUT)
    @PutMapping("/user")
    public String putUser() {
        return "PUT-张三";
    }

//    @RequestMapping(value = "/user", method = RequestMethod.DELETE)
    @DeleteMapping("/user")
    public String deleteUser() {
        return "DELETE-张三";
    }

}
```



#### 普通参数与基本注解

- @PathVariable 路径变量
- @RequestHeader 获取请求头
- @RequestParam 获取请求参数（指问号后的参数，url?a=1&b=2）
- @CookieValue 获取Cookie值
- @RequestAttribute 获取request域属性
- @RequestBody 获取请求体[POST]
- @MatrixVariable 矩阵变量
- @ModelAttribute



#### PathVariable 路径变量

Controller接口路径中输入的值，会直接作为参数应用到method中

接口中的函数为：

```
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String, Object> getCar(
            @PathVariable("id") Integer id,
            @PathVariable("username") String name,
            @PathVariable Map<String, String> pv) {
        Map<String, Object> map = new HashMap<>();
        map.put("id",id);
        map.put("name",name);
        map.put("pv",pv);
        return map;
    }
```

前端输入为：

```
http://localhost:8090/car/3/owner/dog
```

此时输出的结果为：

```
{
	"pv": {
		"id": "3",
		"username": "dog"
	},
	"name": "dog",
	"id": 3
}
```

其中

- @PathVariable("id") Integer id

  会根据格式找到id的所在位置

- @PathVariable Map<String, String> pv

  会将全部的输入以key-value的结构放在一个map中







#### RequestHeader 获取请求头

获取传递参数的请求头

输入Controller接口函数为：

```
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String, Object> getCar(
            @PathVariable("id") Integer id,
            @PathVariable("username") String name,
            @PathVariable Map<String, String> pv,
            @RequestHeader("User-Agent") String userAgent,
            @RequestHeader Map<String, String> header) {
        Map<String, Object> map = new HashMap<>();
        map.put("id",id);
        map.put("name",name);
        map.put("pv",pv);
        map.put("userAgent",userAgent);
        map.put("headers",header);
        return map;
    }
```

前端输入为

```
http://localhost:8090/car/3/owner/dog
```

输出结果为

```
{
	"headers": {
		"host": "localhost:8090",
		"connection": "keep-alive",
		"cache-control": "max-age=0",
		"sec-ch-ua": "\" Not A;Brand\";v=\"99\", \"Chromium\";v=\"100\", \"Google Chrome\";v=\"100\"",
		"sec-ch-ua-mobile": "?0",
		"sec-ch-ua-platform": "\"Windows\"",
		"upgrade-insecure-requests": "1",
		"user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36",
		"accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
		"sec-fetch-site": "none",
		"sec-fetch-mode": "navigate",
		"sec-fetch-user": "?1",
		"sec-fetch-dest": "document",
		"accept-encoding": "gzip, deflate, br",
		"accept-language": "zh-CN,zh;q=0.9",
		"cookie": "Idea-8296eb31=170791c2-e36f-423a-8592-dac1027da86d"
	},
	"pv": {
		"id": "3",
		"username": "dog"
	},
	"name": "dog",
	"userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36",
	"id": 3
}
```





#### RequestParam 获取请求参数

获取接口实际输入的参数

接口内容：

```
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String, Object> getCar(
            @PathVariable("id") Integer id,
            @PathVariable("username") String name,
            @PathVariable Map<String, String> pv,
            @RequestParam("age") Integer age,
            @RequestParam("inters") List<String> inters,
            @RequestParam Map<String, Object> params) {
        Map<String, Object> map = new HashMap<>();
        map.put("id",id);
        map.put("name",name);
        map.put("pv",pv);
        map.put("age", age);
        map.put("inters", inters);
        map.put("params", params);
        return map;
    }
```

前端http请求：

```
http://localhost:8090/car/3/owner/dog?age=10&inters=basketball&inters=game 

单个参数可以在链接后使用?分隔，每个参数可以用&分隔
单个参数为 age=10
数组结构为 inters=basketball&inters=game 变量名一致即可认为是List
```

输出结果：

```
{
	"pv": {
		"id": "3",
		"username": "dog"
	},
	"inters": ["basketball", "game"],
	"name": "dog",
	"id": 3,
	"params": {
		"age": "10",
		"inters": "basketball"
	},
	"age": 10
}
```







#### CookieValue 获取Cookie值

​		HTTP Cookie（也叫Web Cookie或浏览器Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带，发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie使基于无状态的HTTP协议记录稳定的状态成为了可能。

接口内容：

```
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String, Object> getCar(
            @PathVariable("id") Integer id,
            @PathVariable("username") String name,
            @PathVariable Map<String, String> pv,
            @CookieValue("Idea-8296eb31") String _Idea,
            @CookieValue("Idea-8296eb31") Cookie cookie
    ) {
        Map<String, Object> map = new HashMap<>();
        map.put("id", id);
        map.put("name", name);
        map.put("pv", pv);
        map.put("Idea", _Idea);
        System.out.println(cookie.getName() + "===>" + cookie.getValue());
        return map;
    }
```

前端Http请求：

```
http://localhost:8090/car/3/owner/dog
```

注意，后端选择Cookie的时候需要看前端真正传递了什么

![Cookie](C:\Users\Administrator\Desktop\SpringBoot材料\img\Cookie.png)

显示的结果为

```
{
  "Idea": "170791c2-e36f-423a-8592-dac1027da86d",
  "pv": {
    "id": "3",
    "username": "dog"
  },
  "name": "dog",
  "id": 3
}
```





#### RequestBody 获取传入数据

Rest接口最常用的获取数据的注解。

- RequestParam通常用来接收传递来的简单的数据
- RequestBody通常用来接收复杂的数据结构，RequestBody在功能上可以覆盖RequestParam

接口内容：

```
    @PostMapping("/save")
    public Map postMethod(@RequestBody String content) {
        Map<String, Object> map = new HashMap<>();
        map.put("content", content);
        return map;
    }
```

接口不能用简单的http请求输入，PostMan输入为

![测试RequestBody](C:\Users\Administrator\Desktop\SpringBoot材料\img\测试RequestBody.png)







#### 接口转发和RequestAttribute注解

​	接口转发，通常会用 "forward:/success"+@Controller注解来进行重定向。

​	如下在访问goto注解之后就会转发到success上

```
@Slf4j
@Controller
public class RequestController {
    @GetMapping("/goto")
    public String goToPage(HttpServletRequest request) {
        log.info("now is in goToPage method!");
        request.setAttribute("msg", "成功了...");
        request.setAttribute("code", 200);
        log.info("准备准发到/success接口中");
        return "forward:/success";   // 转发到  /success请求
    }

    // ResponseBody写在接口上表示可以直接使用，也可以接受其他接口的跳转，如果不写则不能接受其他接口
    @ResponseBody
    @GetMapping("/success")
    public Map<String, Object> success(
            @RequestAttribute("msg") String msg,
            @RequestAttribute("code") Integer code,
            HttpServletRequest request) {
        Object msg1 = request.getAttribute("msg");
        Map<String, Object> map = new HashMap<>();
        map.put("reqMethod_msg", msg1);
        map.put("annotation_msg", msg);
        map.put("code_msg", code);
        return map;
    }
}
```

前端直接访问即可

```
http://localhost:8090/success
```

需要注意的是@RequestAttribute这个注解，它可以单独提取HttpServletRequest中的值。

HttpServletRequest是访问接口时直接就存在的。



======================================================



### 34级开始  复杂参数如何进行传递

======================================================

**Map**、**Model（map、model里面的数据会被放在request的请求域  request.setAttribute） ** 

  

Errors/BindingResult、**RedirectAttributes（ 重定向携带数据）**



=============================================================

### 35集   自定义对象参数



=============================================================



### 37 数据响应

​	SpringBoot自动引入了Jackson + ResponseBody

​	就可以自动根据名称实例化



### 41 响应式编程

### 42  内容协商



================================================================



### 43 视图解析器 thymeleaf

后台传输一些数据，前端会将后台传输的数据修改前端的页面。

后台的pom文件中必须要引入thymeleaf这个三方库：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

后端的接口：

```
@GetMapping("/goto_success")
public String gotoSuccess(Model model) {
	// model中的数据都会被放在请求域中
	model.addAttribute("msg","你好二狗");
	model.addAttribute("link","https://www.baidu.com/");
	return "success";  // 只用controller，进行一次跳转
}
```

前端的success.html中的数据为：

```
<!DOCTYPE html>

<!-- 加入thymeleaf的名称空间 -->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>

    <!-- thymeleaf可以将后端的数据，传递给前端 -->
    <body>
        <h1 th:text="${msg}">哈哈</h1>
        <h1>
            <a href="https://www.google.com/" th:href="${link}"> 从谷歌去百度 </a> <br>
        </h1>
    </body>
</html>
```

在运行之后，前端的页面信息自动进行了修改，修改结果为：

```
<!DOCTYPE html>

<!-- 加入thymeleaf的名称空间 -->
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>

    <!-- thymeleaf可以将后端的数据，传递给前端 -->
    <body>
        <h1>你好二狗</h1>
        <h1>
            <a href="https://www.baidu.com/"> 从谷歌去百度 </a> <br>
        </h1>
    </body>
</html>
```



thymeleaf还有一个功能，它将所有的/templates文件下的html文件都引入进来，并且可以直接访问到。




















































































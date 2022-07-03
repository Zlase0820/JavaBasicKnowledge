@[toc]
# MySQL



## 参考资料

​	[指令教程](https://www.bilibili.com/video/BV1iq4y1u7vj?p=1&vd_source=10fdf5063f722f83a428e760f8daef7e)

​	[SpringMyBatis63-68](https://www.bilibili.com/video/BV19K4y1L7MT?p=63&vd_source=10fdf5063f722f83a428e760f8daef7e)



## 基本概念









## 基础指令

### 









## 使用MyBatis

​		[官方文档](https://mybatis.org/mybatis-3/zh/getting-started.html)

​		

​		MyBatis其实还是写SQL语句，将Java代码中的参数注入到指令中，再将指令查询到的结果反序列成Java对象中的内容。



​		辅助插件： MyBatisX 1.4.3



### Mapper.xml结构写法

组件使用方式：

- src文件包中
    - beans文件夹放置 数据类型
        - Account.java
    - mapper文件夹放置 对应的接口和需要实现的功能
        - AccountMapper.java
    - pom文件引入三方库

- resources文件包中
    - 放置全局配置文件mybatis-config.xml（可以不写，配置都放置在application中）
    - 放置AccountMapper.xml
    - application.yaml文件中存放spring配置



beans-Account.java文件

```
public class Account {
    private Long id;
    private String userId;
    private Integer money;
}
```



AccountMapper.java文件

```
@Mapper
public interface AccountMapper {

    public Account getAcct(Long id);

}
```



三方库引入

```
<!-- 数据库MyBatis -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.2.0</version>
</dependency>

<!-- 使用JDBC链接数据库 -->
<dependency>
	<groupId>org.mariadb.jdbc</groupId>
	<artifactId>mariadb-java-client</artifactId>
	<version>3.0.5</version>
</dependency>

```



全局配置文件

注意，Spring中，可以不写全局配置文件，所有的配置都可以放置在application.yaml中

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>

```



application.yaml中进行配置

注意，可以将config-location都放置在configration中

```
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    map-underscore-to-camel-case: true

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/zlase?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: root
    driver-class-name: org.mariadb.jdbc.Driver
```



AccountMapper.xml文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.springlearndemo.mapper.AccountMapper">
    <select id="getAcct" resultType="com.example.springlearndemo.beans.Account">
        select * from Blog where id = #{id}
    </select>
</mapper>
```



application配置文件：

```
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/zlase?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: root
    driver-class-name: org.mariadb.jdbc.Driver
```



### 注解混用写法

在注解的Mapper中直接写SQL

```
@Mapper
public interface AccountMapper {
    @Select("select * from city where c_no=#{cNo}")
    public String getAcct(String cNo);
}
```





## 使用MyBatisPlus



mybatisPlus是Mybatis的升级版，使用起来更简单方便，不过常规工程依然是以mybatis为主。


































# Json处理



## 基础知识

最常用的处理Json的Java三方库有：

- Gson
- FastJson
- Jackson



其中Gson和FastJson是功能强大的json处理工具，体量大，功能全面。

Jackson更轻量，但是功能更少，当前被作为默认的Json处理工具集成到SpringBoot中。

工作当中多使用Gson，如果能够让SpringBoot默认使用Gson就会更方便。







## Spring整合Gson

[参考资料](https://blog.csdn.net/Saykuray/article/details/110506500)



- 优先在Spring中去除掉Jackjson相关组件

  ```
  @SpringBootApplication(exclude = {JacksonAutoConfiguration.class})
  ```

- 在Config中注入一个组件，增加一个GsonConfig类，将HttpMessageConverters注入到Bean中

  ```
  import com.google.gson.Gson;
  import com.google.gson.GsonBuilder;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.boot.autoconfigure.http.HttpMessageConverters;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.http.converter.HttpMessageConverter;
  import org.springframework.http.converter.json.GsonFactoryBean;
  import org.springframework.http.converter.json.GsonHttpMessageConverter;
  
  import java.util.ArrayList;
  import java.util.Collection;
  
  @Configuration
  public class GsonConfig {
  
      // 读取配置文件的时间格式
      @Value("${spring.gson.date-format}")
      private String dateFormat;
  
      // 将解析器注入Spring容器，代替默认的解析器
      @Bean
      public HttpMessageConverters customConverters() {
          Collection<HttpMessageConverter<?>> messageConverters = new ArrayList<>();
          GsonHttpMessageConverter converter = new GsonHttpMessageConverter();
          converter.setGson(new GsonBuilder().setDateFormat(dateFormat).create());
          messageConverters.add(converter);
          return new HttpMessageConverters(true, messageConverters);
      }
  
      @Bean
      public GsonFactoryBean gsonFactoryBean() {
          return new GsonFactoryBean();
      }
  
      // 注入普通Gson，后续可以用Autowire直接使用
      @Bean
      public Gson gson(GsonFactoryBean gsonFactoryBean) {
          // 设置日期格式
          gsonFactoryBean.setDateFormatPattern(dateFormat);
          gsonFactoryBean.afterPropertiesSet();
          return gsonFactoryBean.getObject();
      }
  }
  
  ```

- application.yaml中进行配置

  ```
  spring:
    gson:
      date-format: yyyy-MM-dd
  ```

  


















































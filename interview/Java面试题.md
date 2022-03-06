# 面试题目汇总

面试题目只能作为基础知识和图谱中查漏补缺的东西，不能因为考这些东西对基础知识本末倒置。



## 面经传送门

[校招面经](https://github.com/DreamCats/java-notes/tree/master/Java/mianjing)

[在线面试助手小程序](https://github.com/DreamCats/online-interview)

[面经总结](https://github.com/DreamCats/java-notes)

[牛客网海康威视面试题](https://www.nowcoder.com/discuss/836572?type=all&order=recall&pos=&page=1&ncTraceId=&channel=-1&source_id=search_all_nctrack)

[牛客网2023届校招Java面经](https://www.nowcoder.com/discuss/845228?type=all&order=recall&pos=&page=0&ncTraceId=&channel=-1&source_id=search_all_nctrack)



## Java工程知识

###  

### MongoDB数据库

- MongoDB和MySQL，Redis的差别？什么情况下使用呢？

- MongoDB和ElasticSearch的区别？为什么搜索时候MongoDB的效果不如ES呢？

- MongoDB有哪些常用的三方库可以进行连接？

- MongoDB driver

- SpringBoot-mongo-starter

- 双亲委派机制是什么？

  

### MySql数据库

- MongoDB和MySQL，Redis的差别？

- MyBatis，MyBatisPlus和普通JDBC之间的差别是什么？

- 介绍一下MyBatis的一级缓存和二级缓存？

  

### Spring框架

- @Autowired的原理是什么？

  

### Dubbo框架





## Java基础知识



### 基础知识

- ==和equals的区别是什么？ 重写.equals方法，为什么必须要重写hashcode方法？

- 接口和抽象类之间有什么区别呢？

- Java语言三大特性是什么？继承封装多态分别介绍一下？

- 方法的重载(同名不同入参)和重写(Override)有什么区别？

- 类加载器有哪些，能简单介绍一下吗？

- 浅拷贝和深拷贝的区别是什么？

- List，Set和Map有什么区别？如何给List中的内容进行排序？Set存储是否有序？如何排序？

- TreeMap和HashMap的差别？HashMap 什么时候扩容？装载因子和临界值默认是多少？扩容成多大？为什么容量是2的幂次方？
- 怎么实现手机号中间四位用星号表示？
  - 答案：subString，String.replace等都可以





### JVM





### 多线程

- Java中常见的线程池有哪些？他们有什么优点和缺点？

- 线程中的sleep和wait有什么区别？

  

### 设计模式

- 简单介绍一下单例模式？
  - 有什么优点缺点？
  - 懒汉式？饿汉式？

- 简单介绍一下观察者模式？什么情况下更适合使用观察者模式？

  

## 数据结构算法

- 队列和栈的特性？如何用两个队列实现栈？

- 如何判断一个链表中是否有环？

- 排序算法有哪些？
  - 冒泡，插入，快速排序，归并排序，堆排序？是否稳定
  - 快速排序和堆排序的最快时间复杂度都是nlogn，甚至快排有恶化的情况？为什么不用堆排序代替快排呢？(要考虑内存Catch的分部，实际情况中快排效率更高)





## 计算机网络

- 介绍一下Http和Https的区别？
  -  https协议怎么实现？
- 介绍一下三次握手，四次挥手？

- get和post的区别，能否用post完全替代get

- 



## 操作系统

- 进程和线程有什么区别？

- 进程之间如何进行通信？
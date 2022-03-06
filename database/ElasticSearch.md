# ElasticSearch

参考材料：

[基础视频教程传送门](https://www.bilibili.com/video/BV1hh411D7sb?p=2&spm_id_from=pageDriver)

[文档教程传送门](https://github.com/rbmonster/learning-note/blob/master/src/main/java/com/toc/ES.md#33)

[学习笔记传送门](https://blog.csdn.net/u011863024/article/details/115721328)



## 基本概念

ElasticSearch是非常常用的数据库搜索引擎，将非结构化的数据(类似MongoDB的数据)存储到ES中，即可通过搜索快速找到可能想要的值。



### 安装教程

[下载地址传送门](https://www.elastic.co/cn/downloads/elasticsearch)

如下是解压后每个文件夹的功能

| 目录    | 含义           |
| ------- | -------------- |
| bin     | 可执行脚本目录 |
| config  | 配置目录       |
| jdk     | 内置 JDK 目录  |
| lib     | 类库           |
| logs    | 日志目录       |
| modules | 模块目录       |
| plugins | 插件目录       |

文件安装完成后，进入bin，点击 elasticsearch.bat 文件启动 ES 服务 。

注意： 9300 端口为 Elasticsearch 集群间组件的通信端口， 9200 端口为浏览器访问的 [http](https://so.csdn.net/so/search?q=http&spm=1001.2101.3001.7020)协议 RESTful 端口。

验证是否安装成功：打开浏览器，输入地址： http://localhost:9200，测试返回结果，返回结果如下，即可认为安装成功：

```
{
  "name" : "DESKTOP-LNJQ0VF",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "nCZqBhfdT1-pw8Yas4QU9w",
  "version" : {
    "number" : "7.8.0",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "757314695644ea9a1dc2fecd26d1a43856725e65",
    "build_date" : "2020-06-14T19:35:50.234439Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```



### 数据库分类





### 数据存储概念





### 数据类型




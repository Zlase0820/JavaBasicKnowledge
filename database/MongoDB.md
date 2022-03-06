# MongoDB

参考资料：

[视频教程传送门](https://www.bilibili.com/video/BV18s411E78K?p=12&spm_id_from=pageDriver)



## 基本概念

MongoDB是面向文档的数据库，设计采用横向扩展设计，能自动处理跨集群的数据和负载，自动中心分配文档，以及想用户的请求路由链接到正确的机器上。

与RDS不同在于，将RDS中的行概念，转变为DDS中的"文档"模型，减少了文件之间的"关系"更适合用于大数据场景

独特功能：

- 索引：支持通用二级索引，切提供唯一索引、复合索引、地理空间索引及全文索引。

- 聚合：聚合管道。// todo 没有理解这个是什么？

- 特殊的集合类型：适用于将在某个时刻过期的数据，如会话(session)。支持固定大小的集合。

- 文件存储：支持一种非常易用的协议，用于存储大文件和文件元数据。

  

### 数据库分类

数据库的种类很多，主要分成两个大类RDS和DDS：

- RDS(Relational Database Service，关系型数据库)
  - RDS for MySQL(基于mysql开发)
  - RSD for PostgreSQL(基于postgreSQL开发)
- DDS(Document Database Service，非关系型数据库)
  - MongoDB数据库



### MongoDB的安装和配置

[MongoDB的安装](https://www.cnblogs.com/yunquan/p/11174265.html)

[MongoDB的配置](https://www.cnblogs.com/out-of-memory/p/6810411.html)

Windows上的常用客户端：Navicat，MongoDBCompass

具体的流程需要看[MongoDB配置]，给指定数据库建用户的指令：

```
// 登录root权限的用户
use admindb.auth("admin","password");

// 创建数据库，并给数据库创建一个有读写权限的用户
use ballmatchdb.createUser({  
	user: "football",  
	pwd: "password",  
	roles: [{role: "readWrite",db: "ballmatch"}]
})
```



### MmongDB的存储概念

#### 数据库

Database，数据库是一个仓库，可以在数据库中存放集合

#### 集合

Collection，一个集合就是一组文档，类似MySQL中表的概念。

#### 文档

Document，多个键值有序的放置在一起，可以理解成一个文档。多数情况下是若干个key对应一个json的数据结构: key1,key2,key3 —— Json

- 一对一

- 一对多

- 多对多

  

### 数据类型

- null: 用于表示空值或者不存在的字段

- boolean

- 数值： 64位浮点数

- 字符串string

- 日期：不存储时区

- 正则表达式

- 数组

- 内嵌文档：可嵌套其他文档(可能嵌入一些网络地址等信息)

- 对象id：12字节的ID，唯一标识

- 二进制数据

- 代码：JavaScript代码





### _id 和 ObjectId

MongoDB中存储的文档必须有一个_id键(key)，这个键的值可以是任何类型的，默认是个ObjectId对象。

ObjectId的组成：

- 4位时间戳

- 3位机器码: 主机唯一标识符,机器主机名的散列值

- 2位PID: 进程标识符

- 3位计数器：自动增加的计数器，确保相同进程同一秒产生的ObjectId不一样

_id：

- 通常情况下由通常在客户端由驱动程序完成，自动生成，减轻了数据库的负担



## 基本操作-shell

使用指令的方式操作数据库，通常都是在数据库机器上才可以使用指令，实际工作当中能这么使用的机会不多。



### 进入数Mongo

```
mongo
```



### Mongo基本指令

数据库和文档，不需要特意创建，如果不存在且进行操作时，系统会在第一次插入数据的时候创建好库和集合。

进入数据库：

```
show databases
```

进入数据库database1：

```
use database1
```

删除数据库：

```
db.dropDatabase()
```

查看当前在哪个数据库中：

```
db
```

在数据库中查询有多少集合：

```
show collections
```

删除集合

```
db.collectionA.drop()
```



### MongoDB的CRUD指令

[官方指导手册](https://docs.mongodb.com/guides/?_ga=2.60015405.1805493940.1644810053-825664797.1644810053)

[官方文档](https://docs.mongodb.com/v4.4/tutorial/iterate-a-cursor/)



#### 插入文档

[插入指导传送门](https://docs.mongodb.com/v4.4/reference/insert-methods/)

##### 单对象插入(Object+List)

原始模板：

```
db.collection.insertOne()  // 插入一个数据——Object

db.collection.insertMany()  // 插入一组数据——List<Object>

db.collection.insert()    // 相当于insertOne和insertMany的合集
```

向当前数据库，inventory中插入1个文档：

```
db.inventory.insertOne(  {  
	"item" : "canvas",  
    "qty" : 100,  
    "tags" : ["cotton"],  
    "size" : { "h" : 28, "w" : 35.5, "uom" : "cm" }  
})
```

向当前数据库，inventory2中插入10个文档：

```
for(i=0;i<10000;i++) {  
	db.inventory2.insert({      
		"i":i,      
		"username":"username"+i,      
		"age":Math.floor(Math.random()*100),      
		"created":new Date()    
	});
}
```

向当前数据库，inventory中插入1个数组(3个文档)：

```
db.inventory.insert([
	{name:"沙和尚",age:10,gender:"man"},
	{name:"猪八戒",age:20,gender:"man"},
	{name:"女娲",age:30,gender:"woman"}
]);
```



##### 内嵌文档插入

内嵌文档即为复杂些的Json数据，仍然可以按照Object，List或Map形式插入。向当前数据库，books中插入1个内嵌文档：

```
db.books.insertMany( [
   { _id: 1, name: "python", price: 25, size: { h: 14, w: 21}, reading: ["Tom","John"], grade:[1,2,3]},
   { _id: 2, name: "mongo", price: 50, size: { h: 8.5, w: 11}, reading: ["John","Dave"], grade:[7,9]},
   { _id: 3, name: "webGL", price: 80, size: { h: 8.5, w: 11}, reading: ["Lily"], grade:[2,6] },
]);
```



##### 多文档联合插入

通常都是存入其他collection中的主键_id，或者其他特征，保证多个集合可以相互查询

```
db.books.insertMany( [
   { _id: 1, name: "python", price: 25, size: { h: 14, w: 21}, reading: ["Tom","John"], grade:[1,2,3]},
   { _id: 2, name: "mongo", price: 50, size: { h: 8.5, w: 11}, reading: ["John","Dave"], grade:[7,9]},
   { _id: 3, name: "webGL", price: 80, size: { h: 8.5, w: 11}, reading: ["Lily"], grade:[2,6] },
]);

db.price.insertMany([
	{ruler:"tom",bookid:1},
	{ruler:"peter",bookid:2},
	{ruler:"dog",bookid:3},
]);
```





#### 查询文档

[查询指导传送门](https://docs.mongodb.com/v4.4/tutorial/query-documents/)



##### 单文档查询

原始模板：

```
db.collection.find()      // 查找后返回一个数组
db.collection.findOne()   // 查找后返回一个对象
```

预置数据：

```
db.inventory.insert([
	{name:"沙和尚",age:10,gender:"man"},
	{name:"猪八戒",age:20,gender:"man"},
	{name:"女娲",age:30,gender:"woman"},
	{name:"女娲",age:18,gender:"unkonown"}
]);

```

基础查询：

```
db.inventory.find(
	{
		name:"女娲"
	}
)
db.inventory3.findOne(
	{
		name:"女娲",
		age:18
	}
)

```

查询Index：

```
db.inventory.find(
	{
		name:"女娲"
	}
)[0]
```

查询结果的数量：

```
db.inventory.find().count();
```



##### 内嵌文档查询

预置数据

```
db.books.insertMany( [
   { _id: 1, name: "python", price: 25, size: { h: 14, w: 21}, reading: ["Tom","John"], grade:[1,2,3]},
   { _id: 2, name: "mongo", price: 50, size: { h: 8.5, w: 11}, reading: ["John","Dave"], grade:[7,9]},
   { _id: 3, name: "webGL", price: 80, size: { h: 8.5, w: 11}, reading: ["Lily"], grade:[2,6] },
]);

```

字典查询

```
// 查询主属性
db.books.find({name:"python"})

// 查询字典对象
db.books.find({"size.h":14})

// 运算符查询 $lt表示非，查询size.w不包含
db.books.find( { "size.w": { $lt: 21} } );
```

列表查询

```
// 列表精准查询
db.books.find( { reading: ["Tom","John"] });

// 查询列表，查询reading的第一个包含Tom的字段
db.books.find({"reading.0":"Tom"})

// 列表运算符，要求元素存在且不要求顺序 $all
db.books.find( { reading: {$all:["John"]} });

// 列表包含元素 List.contain 只要列表中包含了其中一个元素，就算匹配成功
db.books.find( { reading: "John" });

// 只要列表中有一元素的值大于$gt,还有一元素小于$lt即满足
db.books.find( { grade: { $gt: 5, $lt: 3} });

// 列表中至少有一个元素同时满足$elemMatch内的条件  $gte 大于等于
db.books.find( { grade: { $elemMatch: { $gte: 6, $lt: 8 } } });

// 或查询，列表中grade小于2或大于8的值
db.books.find({
	$or:[{grade:{$gt:8}},{grade:{$lt:2}}]
})

```

##### 多文档联合查询

预置数据

```
db.books.insertMany( [
   { _id: 1, name: "python", price: 25, size: { h: 14, w: 21}, reading: ["Tom","John"], grade:[1,2,3]},
   { _id: 2, name: "mongo", price: 50, size: { h: 8.5, w: 11}, reading: ["John","Dave"], grade:[7,9]},
   { _id: 3, name: "webGL", price: 80, size: { h: 8.5, w: 11}, reading: ["Lily"], grade:[2,6]},
]);

db.price.insertMany([
	{ruler:"tom",bookid:1},
	{ruler:"peter",bookid:2},
	{ruler:"dog",bookid:3},
]);
```

多文档查询

```
// 查询tom管理者的书名 python
var tom_id = db.price.findOne({ruler:"tom"}).bookid
var name = db.books.findOne({_id:tom_id}).name 
name
```



#### 更新文档

[更新指导传送门](https://docs.mongodb.com/v4.4/tutorial/update-documents/)

##### 单文档更新

原始模板：

```
db.collection.update(<filter>, <update>, <options>)          // 默认只更新一个    
db.collection.updateOne(<filter>, <update>, <options>)       // 只更新一个
db.collection.updateMany(<filter>, <update>, <options>)      // 匹配到的全部更新
db.collection.replaceOne(<filter>, <update>, <options>)

<filter>  查询条件
<update>  新对象
<options> 特殊条件
```



##### 修改操作符

```
$set     用来修改文档中的指定属性
$unset   用来删除文档中的指定属性
```

预置数据：

```
db.inventory3.insert([
	{name:"沙和尚",age:10,gender:"man"},
	{name:"猪八戒",age:20,gender:"man"},
	{name:"女娲",age:30,gender:"woman"},
	{name:"女娲",age:18,gender:"unkonown"}
]);
```

整个对象更新：

```
// 直接使用update的缺点是找到对象之后，会直接用新对象替换
db.inventory3.update({
	name:"沙和尚"
},
{
	name:"沙和尚2号",
	age:300,
	gender:"man"
})

```

对象指定属性的更新：

```
// 用"修改操作符"$set，找到对象，针对对象中的属性做升级
db.inventory3.update({
	name:"猪八戒",
	age:20
},
{
	$set:{
		age:60	
	}
})

// 增加一个字段(全部)
db.inventory3.updateMany({
	name:"女娲"
},
{
	$set:{
		address:"tiangong"	
	}
})

// 增加一个字段(全部)
db.inventory3.update({
	name:"女娲"
},
{
	$set:{
		address:"china"	
	}
},
{
	multi:true
})

```



#### 删除文档

[删除指导传送门](https://docs.mongodb.com/v4.4/tutorial/remove-documents/)

原始模板：

```
db.collection.deleteMany()
db.collection.deleteOne()
db.collection.remove()          // 默认删除全部，与deleteMany一致

```



预置数据：

```
db.inventory3.insert([
	{name:"沙和尚",age:10,gender:"man"},
	{name:"猪八戒",age:20,gender:"man"},
	{name:"女娲",age:30,gender:"woman"},
	{name:"女娲",age:18,gender:"unkonown"}
]);

```



删除数据：

精准搜索，删除单个数据或多个数据

```
db.inventory3.deleteOne(
	{
		age:20
	}
)

db.inventory3.deleteMany(
	{
		name:"女娲"
	}
)

```

```
// 只删除一个对象，默认是全部删除
db.inventory3.remove(
	{
		age:10
	},
	true
)
```

```
// 删库跑路
db.inventory3.remove({})

```

```
// 仅对一个对象的一个属性进行删除
db.inventory3.updateOne(
	{
		name:"女娲"
	},
	{
		$unset:{
			gender:"unkonown"
		}
	}
)

```

软删除：

​		通常情况下， 数据直接删除会真正的丢失掉，企业为了防止这种情况，方便回退，则使用软删除的方式。每一个字段增加一个isDel:0的属性，0表示没有被删除，如果需要删除，则将这个属性至为1，这样在查询的时候，只查询isDel为0的就认为没有被删除掉。

```
db.inventory3.insert([
	{name:"沙和尚",age:10,gender:"man",isDel:0},
	{name:"猪八戒",age:20,gender:"man",isDel:0},
	{name:"女娲",age:30,gender:"woman",isDel:0},
	{name:"女娲",age:18,gender:"unkonown",isDel:0}
]);

// 软删除掉 name:"猪八戒"
db.inventory3.updateOne(
	{
		name:"猪八戒"
	},
	{
		$set:{
			isDel:1
		}
	}
)

// 查询结果，软删除掉的数据就不会在显示了
db.inventory3.find(
	{
		isDel:0
	}
)
```



### MongoDB的索引

[索引参考资料](https://www.runoob.com/mongodb/mongodb-indexing.html)

预置数据

```
for(i=0;i<10000;i++) {
    db.helloworld.insert(
        {
            "i":i,
            "username":"username"+i,
            "age":Math.floor(Math.random()*100),
            "created":new Date()
        });
}

```

创建索引

```
// 原始语法，key是字段名称，1为升序创建索引，-1为降序创建索引
db.COLLECTION_NAME.ensureIndex({KEY:1})

// 基于预置数据的age字段创建索引，基于age搜索时效率更高
db.helloworld.ensureIndex({"age":1})
db.helloworld.find({age:20})

// 复合索引：基于预置数据，根据i和username做索引
db.helloworld.ensureIndex({"i":1,"username":-1})
db.helloworld.find({"i":20,"username":"username20"})

```

索引的种类

- 复合索引：多个索引联合简创建

- 覆盖索引：一个数据库查询，索引覆盖了所有查询的字段，概念与Mysql类似。

- 隐式索引：针对复合索引，索引具有最左前缀的特，同mysql

- 索引失效：$nin、$ne 等取反的查询，会影响使用索引 与 mysql类似

- 唯一索引： null也是一个唯一索引的值，不可重复

- 稀疏索引： 与唯一索引一起使用可以解决null值可为重复的，但是如果字段有值则必须为唯一

  

### MongoDB的排序操作

预置数据

```
db.inventory.insert([
	{name:"女娲",age:30,strange:50,gender:"woman"},
	{name:"沙和尚",age:10,strange:80,gender:"man"},
	{name:"猪八戒",age:20,strange:80,gender:"man"}
]);
```



排序查询

```
// sort中1为升序排列，-1为降序排列
// 优先按照stange排序，strange中相同则按照age排序
db.inventory.find().sort({
	strange:1,
	age:-1
})
```



## 基本操作-java

工作中能用指令直接操作数据库的都是运维才有权限的，大多数开发都是用自己的工具或者微服务才能对MongoDB数据库进行操作。



### 基本概念

用Java操作MongoDB数据库需要三方依赖，最常用的有如下两种：

- MongoDB driver： MongoDB官方提出的基于Java的数据库操作三方库

- Spring Data MongoDB： Spring集成了MongoDB driver，方便在微服务中使用的三方依赖



### MongoDB Driver

#### 基本信息

[MongoDB driver 操作传送门](https://docs.mongodb.com/drivers/java/sync/current/usage-examples/)

MongoDB driver是MongoDB官方开发的数据库连接依赖，类似于JDBC，通过特定的方式对MongoDB数据库进行操作，使用起来相对原始。

```
<dependency>  <!-- Mongo Driver -->
	<groupId>org.mongodb</groupId>
	<artifactId>mongodb-driver-sync</artifactId>
	<version>4.4.2</version>
</dependency>

```



#### 连接数据库

链接串

链接MongoDB通常用一个连接串进行连接，注意账号密码中的特殊符号需要进行转义

```
// 连接串模板
mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[database][?options]]
```

```
// 用户名，密码；
// 数据库地址，端口号；
// 数据库名称
// 其他行为
mongodb://username:fassword@127.0.0.1:8848/database?authSource=database
```



链接示例代码

连接数据库，创建一个MongoClient类型，将连接串传到MongoDatabase类型中，即可完成数据库连接，后续针对这个类进行操作，即可实现对Mongo的CRUD。

```
// 连接MongoDB数据库，并插入一个数据
package scripts.mongoOprate;

import com.mongodb.MongoException;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.result.InsertOneResult;

import org.bson.Document;
import org.bson.types.ObjectId;

import java.util.Arrays;

public class Insert {
    public static void main(String[] args) {
        // Replace the uri string with your MongoDB deployment's connection string
        String uri = "mongodb://localhost:27017/";

        // 创建一个MongoClient用来操作数据库
        try (MongoClient mongoClient = MongoClients.create(uri)) {
            MongoDatabase database = mongoClient.getDatabase("temp_data_base");  // 选库
            MongoCollection<Document> collection = database.getCollection("inventory");  // 选集合
            try {
                // 将数据插入到result中，在insertOne中进行插入
                InsertOneResult result = collection.insertOne(new Document()
                    .append("_id", new ObjectId())
                    .append("title", "Ski Bloopers")
                    .append("genres", Arrays.asList("Documentary", "Comedy")));
                System.out.println("Success! Inserted document id: " + result.getInsertedId());
            } catch (MongoException me) {
                System.err.println("Unable to insert due to an error: " + me);
            }
        }
    }
}
```



正常情况下在连接之后需要关闭MongoDB的连接，但是使用try-with的时候可以不用写

```
import com.mongodb.Mongo;
...

Mongo mongoClient = new Mongo(db,port);
...
mongoClient.close();

```



#### 插入文档

​		通常都不会在代码的Document()类中直接append(key,value)，而是使用Json的形式，一次性完成数据结构的初始化，并进行存储。

- 插入单个文档 Object 相当于List中的单独一个文件，后续可以持续进行插入

- 插入一个List

- 插入一个Map



插入单个Object

只插入一个object类型的数据

```
package scripts.mongoOprate;

import com.mongodb.MongoException;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.result.InsertOneResult;

import org.bson.Document;

public class Insert {
    public static void main(String[] args) {
        // Replace the uri string with your MongoDB deployment's connection string
        String uri = "mongodb://localhost:27017/";

        // 插入单个数据，通过Gson将其进行反序列化
        String json = "{ \n" +
            "   \t\"item\" : \"canvas\",\n" +
            "   \t\"qty\" : 100,\n" +
            "   \t\"tags\" : [\"cotton\"],\n" +
            "   \t\"size\" : { \"h\" : 28, \"w\" : 35.5, \"uom\" : \"cm\" }\n" +
            "   }";

        // 创建一个MongoClient用来操作数据库
        try (MongoClient mongoClient = MongoClients.create(uri)) {
            MongoDatabase database = mongoClient.getDatabase("temp_data_base");
            MongoCollection<Document> collection = database.getCollection("inventory");
            InsertOneResult insertOneResult = collection.insertOne(Document.parse(json));
            System.out.println(insertOneResult.getInsertedId());
        } catch (MongoException me) {
            System.err.println("Unable to insert due to an error: " + me);
        }
    }
}
```



插入一个List

```
package scripts.mongoOprate;

import com.google.common.reflect.TypeToken;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.mongodb.MongoException;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.result.InsertManyResult;

import org.bson.Document;

import java.util.ArrayList;
import java.util.List;


public class Insert {
    private static final Gson GSON = new GsonBuilder().setPrettyPrinting().create();

    public static void main(String[] args) {

        // Replace the uri string with your MongoDB deployment's connection string
        String uri = "mongodb://localhost:27017/";

        // 插入一个List，通过Gson将其进行反序列化
        String json = "[\n" +
            "    {\n" +
            "        \"name\": \"沙和尚\",\n" +
            "        \"age\": 10,\n" +
            "        \"gender\": \"man\"\n" +
            "    },\n" +
            "    {\n" +
            "        \"name\": \"猪八戒\",\n" +
            "        \"age\": 20,\n" +
            "        \"gender\": \"man\"\n" +
            "    },\n" +
            "    {\n" +
            "        \"name\": \"女娲\",\n" +
            "        \"age\": 30,\n" +
            "        \"gender\": \"woman\"\n" +
            "    }\n" +
            "]";

        // 处理Json成List
        List<Person> personList = GSON.fromJson(json, new TypeToken<ArrayList<Person>>() {
        }.getType());

        // 创建一个MongoClient用来操作数据库
        try (MongoClient mongoClient = MongoClients.create(uri)) {
            MongoDatabase database = mongoClient.getDatabase("temp_data_base");
            MongoCollection<Document> collection = database.getCollection("inventory");
            List<Document> documentList = new ArrayList<>();
            // 需要转换成Document类型
            for (Person person : personList) {
                documentList.add(Document.parse(GSON.toJson(person)));
            }
            InsertManyResult insertManyResult = collection.insertMany(documentList);
        } catch (MongoException me) {
            System.err.println("Unable to insert due to an error: " + me);
        }
    }

    // 继承Document的类可以直接写入
    static class Person {
        public String name;
        public int age;
        public String gender;

        public Person(String name, int age, String gender) {
            this.name = name;
            this.age = age;
            this.gender = gender;
        }
    }
}

```



插入一个Map

```
package scripts.mongoOprate;

import com.google.common.reflect.TypeToken;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.mongodb.MongoException;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.result.InsertManyResult;
import com.mongodb.client.result.InsertOneResult;

import org.bson.Document;
import org.springframework.web.servlet.handler.HandlerMethodMappingNamingStrategy;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;


public class Insert {
    private static final Gson GSON = new GsonBuilder().setPrettyPrinting().create();

    public static void main(String[] args) {

        // Replace the uri string with your MongoDB deployment's connection string
        String uri = "mongodb://localhost:27017/";

        // 插入单个数据，通过Gson将其进行反序列化
        String json = "{\n" +
            "    \"xiyouji\": [\n" +
            "        {\n" +
            "            \"name\": \"沙和尚\",\n" +
            "            \"age\": 10,\n" +
            "            \"gender\": \"man\"\n" +
            "        },\n" +
            "        {\n" +
            "            \"name\": \"猪八戒\",\n" +
            "            \"age\": 20,\n" +
            "            \"gender\": \"man\"\n" +
            "        }\n" +
            "    ],\n" +
            "    \"other\": {\n" +
            "        \"name\": \"女娲\",\n" +
            "        \"age\": 30,\n" +
            "        \"gender\": \"woman\"\n" +
            "    }\n" +
            "}";

        // 处理Json成List
        Map<String,Object> map = GSON.fromJson(json,new HashMap<String,Object>().getClass());

        // 创建一个MongoClient用来操作数据库
        try (MongoClient mongoClient = MongoClients.create(uri)) {
            MongoDatabase database = mongoClient.getDatabase("temp_data_base");
            MongoCollection<Document> collection = database.getCollection("inventory");
            InsertOneResult insertOneResult = collection.insertOne(new Document(map));
        } catch (MongoException me) {
            System.err.println("Unable to insert due to an error: " + me);
        }
    }
}

```



#### 查询文档

[官方文档传送门](https://docs.mongodb.com/drivers/java/sync/current/usage-examples/findOne/)

```
import com.mongodb.client.model.Filters;

用collection.find(Filters.eq("key","value"))一系列的方式进行搜索

FindIterable<Document> findResult = collection.find(Filters.eq("key","value"));

// 写一个iterator去遍历结果，每一行的结果存储到一个document中
MongoCursor<Document> mongoCursor = findIterable.iterator();
while (mongoCursor.hasNext()) {
	Document document = mongoCursor.next();
	document.keyXXXX
}
```



#### 更新文档

[文档更新传送门](https://docs.mongodb.com/drivers/java/sync/current/usage-examples/updateOne/)

用官网的例子就可以实现更新

```
// Replace the uri string with your MongoDB deployment's connection string
        String uri = "<connection string uri>";
        try (MongoClient mongoClient = MongoClients.create(uri)) {
            MongoDatabase database = mongoClient.getDatabase("sample_mflix");
            MongoCollection<Document> collection = database.getCollection("movies");
            Document query = new Document().append("title",  "Cool Runnings 2");
            Bson updates = Updates.combine(
                    Updates.set("runtime", 99),
                    Updates.addToSet("genres", "Sports"),
                    Updates.currentTimestamp("lastUpdated"));
            UpdateOptions options = new UpdateOptions().upsert(true);
            try {
                UpdateResult result = collection.updateOne(query, updates, options);
                System.out.println("Modified document count: " + result.getModifiedCount());
                System.out.println("Upserted id: " + result.getUpsertedId()); // only contains a value when an upsert is performed
            } catch (MongoException me) {
                System.err.println("Unable to update due to an error: " + me);
            }
        }
```



#### 删除文档

[删除文档传送门](https://docs.mongodb.com/drivers/java/sync/current/usage-examples/deleteOne/)

可以直接参考官方文档

```
package usage.examples;

import static com.mongodb.client.model.Filters.eq;

import org.bson.Document;
import org.bson.conversions.Bson;

import com.mongodb.MongoException;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.result.DeleteResult;

public class DeleteOne {

    public static void main(String[] args) {
        // Replace the uri string with your MongoDB deployment's connection string
        String uri = "<connection string uri>";

        try (MongoClient mongoClient = MongoClients.create(uri)) {

            MongoDatabase database = mongoClient.getDatabase("sample_mflix");
            MongoCollection<Document> collection = database.getCollection("movies");

            Bson query = eq("title", "The Garbage Pail Kids Movie");

            try {
                DeleteResult result = collection.deleteOne(query);
                System.out.println("Deleted document count: " + result.getDeletedCount());
            } catch (MongoException me) {
                System.err.println("Unable to delete due to an error: " + me);
            }
        }
    }
}

```



### Spring Data MongoDB

[Spring Data MongoDB 使用教程传送门](http://www.mydlq.club/article/85/#documentTop)

spring-boot-starter-data-mongodb集成了MongoDB driver和spring-data-mongodb，在原始Spring Driver的基础上进行了扩展，通过注解配置文件等方式，让用户更方便的操作MongoDB数据库。

在启动Spring微服务时，会通过配置文件连接数据库。

```
<dependency>  <!-- Spring MongoDB -->
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-mongodb</artifactId>
	<version>2.6.3</version>
</dependency>

<dependency>   <!-- Spring web -->
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

```



#### 基本信息



#### 连接数据库



#### 插入文档



#### 查询文档



#### 更新文档



#### 删除文档



## MongoDB面经

### MongoDB和MySQL的差别

- Mysql是关系型数据库，而MongoDB是非关系型

- MongoDB文档自然地映射我们的Model，而Mysql通常需要多表关联进行数据映射。

- 横向拓展能力MongoDB可以通过原生分片完善支持，而Mysql只能通过数据分区或者应用侵入式的访问实现分区

- Mongodb的文档字段可以是动态的，而Mysql新增字段则需要写sql进行添加

### MongoDB的应用场景

- 元数据（配置）管理：比如常见的Java Spring中经常需要配置数据，而随着相同类型的数据越来越多，就适合转移到MongoDB中。该类型数据变化快，且经常以点查为主。

- 内容管理：对于营销的邮件短信，通常为存在占位符的大文本。该类型的数据也适合存储在MongoDB中。

- 草稿功能：对于用户认证过程，经常需要分几步填写用户的信息，应用通常会保存用户的草稿信息。该草稿信息在正式提交前除了记忆外没有任何意义，而且经常为一个JSON类型的数据。
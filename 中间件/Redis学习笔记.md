# Redis学习笔记

[![Redis](assets/redis-white.png)](https://redis.io/)

采用Redis 6.2.1版本，内容由浅入深，循序渐进，从Redis的基本概念开启讲解。

内容涵盖：Redis安装与部署、Redis常用数据类型操作和底层结构、Redis客户端Jedis和整合SpringBoot项目、Redis事务和锁，Redis持久化RDB和AOF、Redis主从复制和集群、Redis应用中的问题和解决方案（缓存穿透、击穿、雪崩、分布式锁）、Redis的新数据类型以及Redis 6的新特性等。

视频链接：

[【尚硅谷】Redis 6 入门到精通 超详细 教程](https://www.bilibili.com/video/BV1Rv41177Af)

## NoSQL数据库简介

技术的分类：

1. 解决功能性的问题：Java、JSP、RDBMS、Tomcat、HTML、Linux、JDB、SVN、……
2. 解决扩展性的问题：Struts、Spring、SpringMVC、Hibernate、Mybatis、……
3. 解决性能的问题：NoSQL、Java线程、Hadoop、Nginx、MQ、ElasticSearch、……

NoSQL = Not Only SQL，意即“不仅仅是SQL”，泛指非关系型数据库。

NoSQL不依赖业务逻辑方式存储，而以简单的key-value模式存储，因此大大的增加了数据库的扩展能力。

- 不遵循SQL标准
- 不支持ACID
- 远超于SQL的性能

NoSQL适用场景：

- 对数据高并发的读写
- 海量数据的读写
- 对数据高可扩展性

NoSQL不适用的场景：

- 需要事务支持
- 基于SQL的结构化查询存储，处理复杂的关系
- 用不着SQL和用了SQL也不行的情况

## Redis概述

Redis是一个开源的key-value存储系统。

它支持的value类型相对更多，包括string、list、set、zset和hash等。

这些数据类型都支持push、pop、add、remove等丰富的操作，而且这些操作都是原子性的。

Redis支持各种不同方式的排序。

Redis能够实现master-slave（主从）同步。

应用场景：

- 配合关系型数据库做高速缓存
- 多样的数据结构存储持久化数据

查看默认安装目录下的文件：

> redis-benchmark：性能测试工具
>
> redis-check-aof：修复有问题的AOF文件
>
> redis-check-dump：修复有问题的dump.rdb文件
>
> redis-sentinel：Redis集群使用
>
> redis-server：Redis服务器启动命令
>
> redis-cli：客户端，操作入口

Redis相关知识

端口`6379`	从何而来：Alessia `Merz`

默认16个数据库，类似数组下标从0开始，初识默认使用0号库

使用命令`select <dbid>`来切换数据库。如：select 9

统一密码管理，所有库使用同样的密码

> dbsize ：查看当前数据库key的数量

> flushdb：清空当前库

> flushall：通杀全部库

Redis是单线程+多路IO复用

## 常用五大数据类型

### 键（Key）操作

> 查看当前库所有key：keys *
>
> 判断key是否存在：exists key的名称
>
> 查看key的类型：type key名称
>
> 删除指定key的数据：del key的名称
>
> 根据value选择非阻塞删除（仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作中）：unlink key的名称
>
> 为key设置过期时间（时间以秒为单位）：expire key 10
>
> 查看key还有多少秒过期（-1表示永不过期，-2表示已经过期）：ttl key

### 字符串（String）

String是Redis最基本的类型，是二进制安全的，意味着Redis的String可以包含任何数据。比如，jpg图片或者序列化对象。

一个Redis中字符串value最多可以是512M

常用操作

```
set <key> <value> 添加键值对

get <key> 查询对应键值

append <key> <value> 将给定的<value>追加到原值的末尾

strlen <key> 获得值的长度

setnx <key> <value> 只有key不存在时，设置key的值

incr <key> 数字值加一，只能对数字值操作，如果为空，新增值为1

decr <key> 数字值减一，只能对数字值操作，如果为空，新增值为-1

incrby/decrby <key> <步长> 将key中存储的数字值增减，自定义步长

mset <key1> <value1> <key2> <value2> ... 同时设置一个或多个key-value对

mget <key1> <key2> ... 同时获取一个或多个value

msetnx <key1> <value1> <key2> <value2> ... 同时设置一个或多个key-value对，当且仅当所有给定的key都不存在

getrange <key> <起始位置> <结束位置> 获取值的范围 前包 后包

setrange <key> <起始位置> <value> 用<value>覆写<key>所存储的字符串值，从<起始位置>开始（索引从0开始）

setex <key> <过期时间> <value> 设置键值的同时，设置过期时间，单位秒

getset <key> <value> 以新换旧，设置了新值同时获得旧值

```

String的数据结构为简单动态字符串（Simple Dynamic String，缩写SDS），是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式，来减少内存的频繁分配。

![image-20220112141342397](assets/image-20220112141342397.png)

需要注意的是字符串最大长度为512M。

### 列表（List）

Redis列表是简单的字符串列表，按照插入顺序排序。可以在列表的头部或者尾部添加一个元素。

它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标操作中间结点性能会较差。

![image-20220113163116708](assets/image-20220113163116708.png)

单键多值

常用命令

```
lpush/rpush <key> <value1> <value2> ... 从左边/右边插入一个或多个值

lpop/rpop <key> 从左边/右边吐出一个值，值在键在，值光键亡

rpoplpush <key1> <key2> 从key1列表右边吐出一个值，插到<key2>列表的左边

lrange <key> <start> <stop> 按照索引下标获得元素（从左到右）

lindex <key> <index> 按照索引下标获得元素（从左到右）

llen <key> 获得列表长度

linsert <key> before <value> <newValue> 在<value>的后面插入<newValue>插入值

lrem <key> <n> <value> 从左边删除n个value（从左到右）

lset <key> <index> <value> 将列表key下标为index的值替换成value
```

List的数据结构为快速链表

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是压缩列表，它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量较大时才会成为快速链表。

Redis将链表和压缩列表结合起来组成了快速链表，这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

### 集合（Set）

Redis的set和list类似，特殊之处在于set可以自动排重，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择。

Set是String类型的无序集合，它的底层其实是一个value为null的hash表，所以添加、删除、查找的复杂度都是O(1)

常用命令

```
sadd <key> <value1> <value2> ... 将一个或多个元素加入集合key中

smembers <key> 取出该集合的所有值

sismember <key> <value> 判断集合<key>是否为含有该<value>值，有1，没有0

scard <key> 返回该集合的元素个数

srem <key> <value1> <value2> ... 删除集合中的某个元素

spop <key> 随机从该集合中吐出一个值

srandmember <key> <n> 随机从该集合中取出n个值，不会从集合中删除

smove <source> <destination> <value> 把集合中的一个值从一个集合移动到另一个集合

sinter <key1> <key2> 返回两个集合的交集

sunion <key1> <key2> 返回两个集合的并集

sdiff <key1> <key2> 返回两个集合的差集
```

Set数据结构是dict字典，字典是用哈希表实现的。

### 哈希（Hash）

Redis Hash是一个键值对集合，是一个string类型的field和value的映射表，hash特别适合于存储对象。

类似Java里面的`Map<String, Object>`

常用命令

```
hset <key> <field> <value> 给<key>集合中的<field>键赋值<value>

hget <key> <field> 从<key>集合<field>取出value

hmset <key1> <field1> <value1> ... 批量设置hash值

hexists <key> <field> 查看哈希表key中，给定field是否存在

hkeys <key> 列出该hash集合的所有field

hvals <key> 列出该hash集合的所有value

hincrby <key> <field> <increment> 为哈希表key中的域field的值加上增量

hsetnx <key> <field> <value> 将哈希表key中的域field的值设置为value，当且仅当域field不存在
```

Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）

当filed-value长度较短且个数较少时，使用ziplist，否则使用hashtable。

### 有序集合（Zset）

Redis有序集合zset和普通集合set非常相似，是一个没有重复元素的字符串集合。

有序集合的每个成员都关联了一个评分，这个评分被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复的。

因为元素是有序的，你也可以很快的根据评分或者次序来获取一个范围的元素。

访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表。

常用命令

```
zadd <key> <score1> <value1> <score2> <value2> ... 将一个或多个member元素及其score值加入到有序集key当中

zrange <key> <start> <stop> [WITHSCORES] 返回有序集key中，下标在<start> <stop> 之间的元素

zrangebyscore key minmax [withscores][limit offset count] 返回有序集key中，所有score值介于min和max之间（包括等于min或max）的成员。有序集成员按score值递增（从小到大）次序排列。

zrevrangebyscore key maxmin [withscores][limit offset count] 同上，改为从大到小排列

zincrby <key> <increment> <value> 为元素的score加上增量

zrem <key> <value> 删除该集合下，指定值的元素

zcount <key> <min> <max> 统计该集合，分数区间内的元素个数

zrank <key> <value> 返回该值在集合中的排名，从0开始
```

SortedSet（zset）是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构`Map<String,Double>`，可以给每一元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。

zset 底层使用了两个数据结构

（1）hash，作用是关联元素value和权重score，保障value的唯一性，可以通过元素value找到相应的score值

（2）跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表

## 配置文件

### Units单位

配置文件开头定义了一些基本的度量单位，只支持bytes，不支持bit，大小写不敏感

### 网络相关配置

bind

默认情况bind=127.0.0.1，只能接受本机的访问请求

不写的情况下，无限制接受任何ip地址的访问

生产环境肯定要写你应用服务器的地址，服务器是需要远程访问的，所以需要将其注释掉

如果开启了protected-mode，那么在没有设定bind ip且没有设密码的情况下，Redis只允许接受本机的响应

端口号，默认6379

tcp-backlog

设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列+已经完成三次握手队列

在高并发环境下，需要一个高的backlog值来避免慢客户连接问题

timeout：超时时间

tcp-keepalive：心跳时间

### 通用配置

daemonize：是否后台启动

pidfile：存放pid文件的位置，每个实例会产生一个不同的pid文件

loglevel：日志级别（debug、verbose、notice、warning）

logfile：日志输出文件的路径

databases：数据库id

### 安全配置

访问密码的查看、设置和取消

在命令中设置的密码，只是临时的，重启Redis服务器，密码就还原了

永久设置，需要在配置文件中进行设置

```bash
config get requirepass

config set requirepass "123456"
```

### LIMITS限制

maxclients：设置redis同时可以与多少个客户端进行连接，默认情况下为10000个客户端，如果达到了此限制，redis则会拒绝新的连接请求，并且向这些连接请求发出"max number of clients reached"

maxmemory：建议必须设置，否则，将内存占满，造成服务器宕机

maxmemory-policy：设置移除策略

![image-20220114184354407](assets/image-20220114184354407.png)

maxmemory-samples：设置样本数量，一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小

## 发布和订阅

Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息

Redis客户端可以订阅任意数量的频道

![image-20220114185006667](assets/image-20220114185006667.png)

注意：发布的消息没有持久化，只能收到订阅后发布的消息

## Redis6新数据类型

### Bitmaps

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作

（1）Bitmaps本事不是一种数据类型，实际上它就是字符串（key-value），但是它可以对字符串的位进行操作

（2）Bitmaps单独提供了一套命令，所以在Redis中使用Bitmaps和使用字符串的方法不太相同。可以把Bitmaps看成一个以位为单位的数组，数组的每个单元只能存储0和1，数组的下标在Bitmaps中称作偏移量

在第一次初识化Bitmaps时，假如偏移量非常大，那么整个初始化过程执行会比较慢，可能会造成Redis的阻塞。

命令

```
setbit <key> <offset> <value> 设置Bitmaps中某个偏移量的值（0或1）offset从0开始

getbit <key> <offset> 获取Bitmaps中某个偏移量的值

bitcount <key> [start end] 统计比特值为1的数量

bitop 是一个复合操作，它可以做多个Bitmaps的and、or、not、xor操作并将结果保存在destkey中
```

### HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog的优点是，在输入元素的数量或体积非常大时，计算基数所需的空间是固定的，并且是很小的。

HyperLogLog只会根据输入元素来计算基数，而不会存储输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素。

命令

```
pfadd <key> <element> [element...] 添加指定元素到HyperLogLog中，成功返回1，失败返回0

pfcount <key> [key...] 计算HLL的近似基数

pfmerge <key> <sourceKey> [sourceKey...] 将一个或多个HLL合并后的结果存储在另一个HLL中
```

### Geospatial

Redis 提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作

命令

```
geoadd <key> <longitude> <latitude> <member> [longitude latitude member...] 添加地理位置（经度、纬度、名称）

geopos <key> <member> [member...] 获得指定地区的坐标值

geodist <key> <member1> <member2> [m|km|ft|mi] 获取两个位置之间的直线距离

georadius <key> <longitude> <latitude> radius m|km|ft|mi 以给定的经纬度为中心，找出某一半径内的元素
```

## Jedis操作Redis6

### Jedis所需的jar包

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
</dependency>
```

### 连接Redis注意事项

禁用Linux的防火墙：Linux（CentOS 7）里执行命令

```bash
systemctl stop/disable firewalld.service
```

### 测试连接

```java
package com.example;

import redis.clients.jedis.Jedis;

public class JedisDemo1 {
    public static void main(String[] args) {
        // 创建Jedis对象
        Jedis jedis = new Jedis("192.168.108.128", 6379);
        // 测试
        String ping = jedis.ping();
        System.out.println(ping);
    }
}

```

### Jedis常用操作

操作key

```java
@Test
public void demo1() {
    Jedis jedis = new Jedis("192.168.108.128", 6379);
    // 添加
    jedis.set("name", "liuyuhe");
    // 获取
    String name = jedis.get("name");
    System.out.println(name);
    // 设置多个key
    jedis.mset("k1", "v1", "k2", "v2");
    // 获取多个key
    List<String> strings = jedis.mget("k1", "k2");
    System.out.println(strings);
    // 查看key
    Set<String> keys = jedis.keys("*");
    for (String key : keys) {
        System.out.println(key);
    }
}
```

List

```java
@Test
public void demo2() {
    Jedis jedis = new Jedis("192.168.108.128", 6379);
    jedis.lpush("key1", "v1", "v2", "v3", "v4");
    List<String> list = jedis.lrange("key1", 0, -1);
    System.out.println(list);
}
```

Set

```java
@Test
public void demo3() {
    Jedis jedis = new Jedis("192.168.108.128", 6379);
    jedis.sadd("names", "tom", "sam", "jack");
    Set<String> names = jedis.smembers("names");
    System.out.println(names);
}
```

Hash

```java
@Test
public void demo4() {
    Jedis jedis = new Jedis("192.168.108.128", 6379);
    jedis.hset("users", "age", "18");
    String age = jedis.hget("users", "age");
    System.out.println(age);
}
```

Zset

```java
@Test
public void demo5() {
    Jedis jedis = new Jedis("192.168.108.128", 6379);
    jedis.zadd("china", 100d, "shanghai");
    jedis.zadd("china", 99d, "beijing");
    Set<String> china = jedis.zrange("china", 0, -1);
    System.out.println(china);
}
```

### 案例-模拟验证码发送

要求：

1、输入手机号，点击发送后，随机生成6位数字码，2分钟有效

2、输入验证码，点击验证，返回成功或失败

3、每个手机号每天只能输入3次

具体实现：

1、生成随机6位数字验证码，可以使用Java中的Random

2、验证码在2分钟内有效，把验证码放到Redis里面，设置过期时间120秒

3、判断验证码是否一致，从redis获取验证码和输入的验证码进行比较

4、每个手机每天只能发送3次验证码，使用incr操作，每次发送后加一，超过3后，不能再发送

```java
package com.example;

import redis.clients.jedis.Jedis;

import java.util.Random;

public class PhoneCode {
    public static void main(String[] args) {
        // 模拟验证码发送
        setRedisCode("15866154198");
        // 校验
        verifyCode("15866154198", "123456");
    }

    // 验证码校验
    public static void verifyCode(String phone, String code) {
        Jedis jedis = new Jedis("192.168.108.128", 6379);
        String codeKey = "VerifyCode" + phone + ":code";
        String redisCode = jedis.get(codeKey);
        if (redisCode.equals(code)) {
            System.out.println("验证码正确");
        } else {
            System.out.println("验证码错误");
        }
    }

    // 每个手机每天只能发送3次，验证码放到redis中，设置过期时间
    public static void setRedisCode(String phone) {
        Jedis jedis = new Jedis("192.168.108.128", 6379);
        String countKey = "VerifyCode" + phone + ":count";
        String codeKey = "VerifyCode" + phone + ":code";
        // 每个手机每天只能发送三次
        String count = jedis.get(countKey);
        if (count == null) {
            jedis.setex(countKey, 24*60*60, "1");
        } else if (Integer.parseInt(count) <= 2) {
            jedis.incr(countKey);
        } else if (Integer.parseInt(count) > 2) {
            System.out.println(phone + ": 今天发送次数已经超过三次");
            jedis.close();
            return;
        }
        // 验证码放到redis中
        String vCode = getCode();
        System.out.println("验证码: " + vCode);
        jedis.setex(codeKey, 120, vCode);
        jedis.close();
    }

    // 生成6位数字验证码
    public static String getCode() {
        Random random = new Random();
        StringBuilder code = new StringBuilder();
        for (int i = 0; i < 6; i++) {
            int v = random.nextInt(10);
            code.append(v);
        }
        return code.toString();
    }
}

```

## Spring Boot 整合 Redis

引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

redis配置

```yaml
spring:
  redis:
    # Redis服务器地址
    host: 192.168.108.128
    # Redis服务器连接端口
    port: 6379
    # 数据库索引
    database: 0
    # 连接超时时间
    timeout: 1800000
    lettuce:
      pool:
        # 最大连接数
        max-active: 20
        # 最大阻塞时间
        max-wait: 1
        # 最大空闲连接
        max-idle: 5
        # 最小空闲连接
        min-idle: 0
```

添加Redis配置类

```java
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        template.setKeySerializer(redisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setConnectionFactory(factory);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        RedisCacheConfiguration configuration = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        return RedisCacheManager.builder(factory)
                .cacheDefaults(configuration)
                .build();
    }
}
```

测试

```java
@RestController
@RequestMapping("/redisTest")
public class RedisTestController {
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @GetMapping
    public String testRedis() {
        // 设置值
        redisTemplate.opsForValue().set("name", "lyh");
        // 获取值
        return (String) redisTemplate.opsForValue().get("name");
    }
}
```



## 参考资料

[【尚硅谷】Redis 6 入门到精通 超详细 教程](https://www.bilibili.com/video/BV1Rv41177Af)

[redis官网](https://redis.io/)


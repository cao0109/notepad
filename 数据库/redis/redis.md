## Redis + Spring Cache

### 1. Redis

Redis 是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

Redis 通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted
sets)等类型。

Redis 与其他 key - value 缓存产品有以下三个特点：

* Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
* Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
* Redis支持数据的备份，即master-slave模式的数据备份。

#### 1.1. Redis 安装

##### 1.1.1. 下载

Redis 的官方网站：https://redis.io/

Redis 的下载地址：https://redis.io/download

##### 1.1.2. 安装

Redis 的安装非常简单，只需要将下载的压缩包解压即可。

##### 1.1.3. 启动

Redis 的启动非常简单，只需要进入到解压后的目录，执行 redis-server.exe 即可。

##### 1.1.4. 连接

Redis 的连接非常简单，只需要进入到解压后的目录，执行 redis-cli.exe 即可。

#### 1.2. Redis 基本命令

##### 1.2.1. 字符串

* set key value：设置指定 key 的值。
* get key：获取指定 key 的值。
* getset key value：将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
* mset key value [key value ...]：同时设置一个或多个 key-value 对。


# Redis

## 1. Redis简介

Redis是一个开源的使用ANSI
C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。它通常被称为数据结构服务器，因为值（value）可以是字符串(
String), 哈希(Hash), 列表(list), 集合(sets)和有序集合(sorted sets)等类型。

Redis支持的数据类型：

- String
- Hash
- List
- Set
- Sorted Set
- Bitmaps
- HyperLogLogs
- Geospatial Indexes
- Streams
- ...

## 自定义Redis的序列化与反序列化

> 采用阿里巴巴的fastjson作为序列化与反序列化的工具

### 1.1. 自定义RedisTemplate

```java

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        // 使用fastjson序列化
        FastJsonRedisSerializer<Object> fastJsonRedisSerializer = new FastJsonRedisSerializer<>(Object.class);
        // value值的序列化采用fastJsonRedisSerializer
        template.setValueSerializer(fastJsonRedisSerializer);
        template.setHashValueSerializer(fastJsonRedisSerializer);
        // key的序列化采用StringRedisSerializer
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.afterPropertiesSet();
        return template;
    }
}
```

### 1.2. 自定义RedisTemplate的序列化与反序列化

```java

public class FastJsonRedisSerializer<T> implements RedisSerializer<T> {

    private static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");

    private Class<T> clazz;

    public FastJsonRedisSerializer(Class<T> clazz) {
        super();
        this.clazz = clazz;
    }

    @Override
    public byte[] serialize(T t) throws SerializationException {
        if (null == t) {
            return new byte[0];
        }
        return JSON.toJSONString(t, SerializerFeature.WriteClassName).getBytes(DEFAULT_CHARSET);
    }

    @Override
    public T deserialize(byte[] bytes) throws SerializationException {
        if (null == bytes || bytes.length <= 0) {
            return null;
        }
        String str = new String(bytes, DEFAULT_CHARSET);
        return (T) JSON.parseObject(str, clazz);
    }
}
```

### 报错：com.alibaba.fastjson.JSONObject cannot be cast to cn.caoh2.app.entity.JwtUser

### com.alibaba.fastjson.JSONException: autoType is not support. cn.caoh2.app.entity.

```java
public class FastJsonRedisSerializer<T> implements RedisSerializer<T> {

    static {
        ParserConfig.getGlobalInstance().addAccept("cn.caoh2.app.entity.");
    }
}
```



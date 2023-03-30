# MyBatis-Plus 使用笔记

Mybatis-plus是一个基于Mybatis的增强工具，可以在Mybatis的基础上提供更方便的CRUD操作，以及一些常用的功能。
它只对Mybatis的SQL进行增强，不会改变Mybatis的原生使用方式。
官方文档地址：[https://baomidou.com/guide/](https://baomidou.com/guide/)

## 1. 添加依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>${mybatis-plus.version}</version>
</dependency>
```

其中，${mybatis-plus.version}为Mybatis-plus的版本号，可在Maven仓库或官方网站上查看最新版本。

## 2. 配置

### 2.1. 配置数据源

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver # 8.0以上版本指定驱动
```

### 2.2. 配置Mybatis-plus

```yaml
mybatis-plus:
  mapper-locations: classpath:mapper/*.xml # mapper文件路径
  type-aliases-package: com.example.demo.entity # 实体类路径
  global-config:
    db-config:
      table-prefix: tb_ # 表前缀
      id-type: auto # 主键生成策略 
      logic-delete-field: deletedFlg # 逻辑删除字段
      logic-delete-value: 1 # 逻辑删除值
      logic-not-delete-value: 0 # 逻辑未删除值
  configuration:
    map-underscore-to-camel-case: true # 开启驼峰命名
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 打印sql语句
```

### 2.3. 分页插件

```java

@Configuration
public class MybatisPlusConfig {
    /**
     * 分页插件
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

### 2.4. 字段自动填充

```java

@Configuration
public class MybatisPlusConfig {
    /**
     * 字段填充
     */
    @Bean
    public MetaObjectHandler metaObjectHandler() {
        return new MetaObjectHandler() {
            @Override
            public void insertFill(MetaObject metaObject) {
                Object createTime = getFieldValByName("createTime", metaObject);
                if (null == createTime) {
                    setFieldValByName("createTime", LocalDateTime.now(), metaObject);
                }
            }

            @Override
            public void updateFill(MetaObject metaObject) {
                Object updateTime = getFieldValByName("updateTime", metaObject);
                if (null == updateTime) {
                    setFieldValByName("updateTime", LocalDateTime.now(), metaObject);
                }
            }
        };

    }
}
```

## 3. 常用注解

### 3.1. 实体类注解

#### 3.1.1. @TableName

用于指定表名，如果实体类名与表名一致，则不需要添加该注解。

```java

@TableName("tb_user")
public class User {
    // ...
}
```

#### 3.1.2. @TableId

用于指定主键，如果主键名与实体类属性名一致，则不需要添加该注解。

```java
public class User {
    // ...
    @TableId(value = "id", type = IdType.AUTO)
    private Long id;
}
```

注意：如果主键名为id，则可以省略value属性。

type属性用于指定主键生成策略，可选值为：

- AUTO：数据库ID自增
- INPUT：用户输入ID
- NONE：该类型为未设置主键类型（默认配置）
- ASSIGN_ID：分配ID（主键类型为Number(Long和Integer)）; eg: 100 101 102
- ASSIGN_UUID：分配UUID（主键类型为String）; eg: 402880e8606c9c2e01606c9c2e3c0000

#### 3.1.3. @TableField

```java
public class User {
    // ...
    @TableField("user_name")
    private String userName;
}
```

属性：

- value：字段名
- exist：是否为数据库表字段
- fill：字段填充策略（可选值为：INSERT、UPDATE、INSERT_UPDATE）
- select：是否查询该字段
- insertStrategy：插入策略（可选值为：IGNORED、NOT_NULL、NOT_EMPTY）
- updateStrategy：更新策略（可选值为：IGNORED、NOT_NULL、NOT_EMPTY）
- whereStrategy：自定义更新条件策略（可选值为：IGNORED、NOT_NULL、NOT_EMPTY）
- numericScale：数值精度

#### 3.1.4. @TableLogic

用于指定逻辑删除字段。

```java
public class User {
    // ...
    @TableLogic
    private Integer deletedFlg;
}
```

#### 3.1.5. @Version

用于指定乐观锁字段。

```java
public class User {
    // ...
    @Version
    private Integer version;
}
```

### 3.2. Mapper接口注解

#### 3.2.1. @Mapper

用于指定Mapper接口。

```java

@Mapper
public interface UserMapper extends BaseMapper<User> {
    // ...
}
```

#### 3.2.2. @CacheNamespace

用于指定二级缓存。

```java

@CacheNamespace
public interface UserMapper extends BaseMapper<User> {
    // ...
}
```

#### 3.2.3. @CacheNamespaceRef

用于指定二级缓存。

```java

@CacheNamespaceRef(UserMapper.class)
public interface UserMapper extends BaseMapper<User> {
    // ...
}
```

> 以上两者的区别在于，@CacheNamespaceRef只能引用一个Mapper接口，而@CacheNamespace可以引用多个Mapper接口。

#### 3.2.4. 增删改查注解

- @Select
- @Insert
- @Update
- @Delete
- @SelectProvider
- @InsertProvider
- @UpdateProvider
- @DeleteProvider
- @Options

@SelectProvider、@InsertProvider、@UpdateProvider、@DeleteProvider用于自定义SQL语句。
根据传入的参数生成对应的SQL语句。

```java
public interface UserMapper extends BaseMapper<User> {
    @SelectProvider(type = UserSqlProvider.class, method = "selectUsersByAge")
    List<User> selectUsersByAge(@Param("age") Integer age);
}

public class UserSqlProvider {
    public String selectUsersByAge(Integer age) {
        return "select * from user where age > " + age + " order by create_time desc";
    }
}

```

@Options用于指定执行SQL语句后的操作。

```java
public interface UserMapper extends BaseMapper<User> {
    @Insert("insert into user (name, age) values (#{name}, #{age})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void insertUser(User user);
}
```

其中，useGeneratedKeys属性表示是否返回自增主键，keyProperty属性表示返回的自增主键赋值给实体类的哪个属性。

## 4. 常用方法








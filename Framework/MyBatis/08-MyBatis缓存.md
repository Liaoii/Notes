## 8. MyBatis缓存

### 8.1 一级缓存



**CacheMapper**：

```java
public interface CacheMapper {
    Emp getEmpByEid(@Param("eid") Integer eid);
}
```

**CacheMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.CacheMapper">
    <select id="getEmpByEid" resultType="Emp">
        select * from t_emp where eid = #{eid}
    </select>
</mapper>
```

​	**CacheTest**：

```java
public class CacheTest {
    @Test
    public void test01() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        CacheMapper mapper = session.getMapper(CacheMapper.class);
        Emp emp1 = mapper.getEmpByEid(1);
        System.out.println(emp1);
        Emp emp2 = mapper.getEmpByEid(1);
        System.out.println(emp2);
    }
}
```

**结果**：

```
DEBUG 07-03 22:25:35,280 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-03 22:25:35,323 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-03 22:25:35,342 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
```

总结：

一级缓存默认为开启状态；一级缓存的范围是SqlSession

### 8.2 使一级缓存失效的四种情况

（1）不同的SqlSession对应不同的一级缓存

（2）同一个SqlSession但是查询条件不同

（3）同一个SqlSession两次查询期间执行了任何一次增删改操作

（4）同一个SqlSeesion两次查询期间手动清空了缓存

**CacheMapper**：

```java
public interface CacheMapper {
    Emp getEmpByEid(@Param("eid") Integer eid);
    int insertEmp(Emp emp);
}
```

**CacheMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.CacheMapper">
    <select id="getEmpByEid" resultType="Emp">
        select * from t_emp where eid = #{eid}
    </select>

    <insert id="insertEmp">
        insert into t_emp values (null, #{empName}, #{age}, #{sex}, #{email}, null)
    </insert>
</mapper>
```

**CacheTest**：

```java
public class CacheTest {
    @Test
    public void test02() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        CacheMapper mapper = session.getMapper(CacheMapper.class);
        Emp emp1 = mapper.getEmpByEid(1);
        System.out.println(emp1);
        mapper.insertEmp(new Emp(null, "ceshi", 22, "女", "122@qq.com"));
        Emp emp2 = mapper.getEmpByEid(1);
        System.out.println(emp2);
    }
}
```

**结果**：

```
DEBUG 07-04 09:19:51,121 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,167 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,197 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
DEBUG 07-04 09:19:51,200 ==>  Preparing: insert into t_emp values (null, ?, ?, ?, ?, null) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,202 ==> Parameters: ceshi(String), 22(Integer), 女(String), 122@qq.com(String) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,208 <==    Updates: 1 (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,209 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,209 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:19:51,210 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
```

**清空缓存**：

```java
public class CacheTest {
    @Test
    public void test03() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        CacheMapper mapper = session.getMapper(CacheMapper.class);
        Emp emp1 = mapper.getEmpByEid(1);
        System.out.println(emp1);
        session.clearCache();
        Emp emp2 = mapper.getEmpByEid(1);
        System.out.println(emp2);
    }
}
```

### 8.3 二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；

二级缓存开启的条件：

（1）在核心配置文件中设置全局配置属性cacheEnabled="true"，默认为true，不需要设置

（2）在映射文件中设置标签\<cache\>

（3）二级缓存必须在SqlSession关闭或提交之后有效，在SqlSession未提交时会保存到一级缓存中

（4）查询的数据所转换的实体类类型必须实现序列化的接口

使二级缓存失效的情况：

​	两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

**Emp**：

```java
public class Emp implements Serializable {
	...
}
```

**CacheMapper**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.CacheMapper">
    
    <cache></cache>
    
    <select id="getEmpByEid" resultType="Emp">
        select * from t_emp where eid = #{eid}
    </select>

    <insert id="insertEmp">
        insert into t_emp values (null, #{empName}, #{age}, #{sex}, #{email}, null)
    </insert>
</mapper>
```

**CacheTest**：没有进行SqlSession关闭，所以缓存不生效

```java
public class CacheTest {
    @Test
    public void test04() {
        try {
            InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
            SqlSession session1 = factory.openSession(true);
            CacheMapper mapper1 = session1.getMapper(CacheMapper.class);
            Emp emp1 = mapper1.getEmpByEid(1);
            System.out.println(emp1);
            
            SqlSession session2 = factory.openSession(true);
            CacheMapper mapper2 = session2.getMapper(CacheMapper.class);
            Emp emp2 = mapper2.getEmpByEid(1);
            System.out.println(emp2);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**结果**：

```
DEBUG 07-04 09:56:23,007 Cache Hit Ratio [com.liaoii.mybatis.mapper.CacheMapper]: 0.0 (LoggingCache.java:60) 
DEBUG 07-04 09:56:23,269 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:56:23,301 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:56:23,322 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
DEBUG 07-04 09:56:23,323 Cache Hit Ratio [com.liaoii.mybatis.mapper.CacheMapper]: 0.0 (LoggingCache.java:60) 
DEBUG 07-04 09:56:23,329 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:56:23,330 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:56:23,335 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
```

**CacheTest**：进行SqlSession关闭，缓存生效

```java
public class CacheTest {
    @Test
    public void test04() {
        try {
            InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
            SqlSession session1 = factory.openSession(true);
            CacheMapper mapper1 = session1.getMapper(CacheMapper.class);
            Emp emp1 = mapper1.getEmpByEid(1);
            System.out.println(emp1);
            session1.close();

            SqlSession session2 = factory.openSession(true);
            CacheMapper mapper2 = session2.getMapper(CacheMapper.class);
            Emp emp2 = mapper2.getEmpByEid(1);
            System.out.println(emp2);
            session2.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**结果**：

```
DEBUG 07-04 09:58:23,314 Cache Hit Ratio [com.liaoii.mybatis.mapper.CacheMapper]: 0.0 (LoggingCache.java:60) 
DEBUG 07-04 09:58:23,631 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:58:23,675 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-04 09:58:23,691 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
WARN  07-04 09:58:23,701 As you are using functionality that deserializes object streams, it is recommended to define the JEP-290 serial filter. Please refer to https://docs.oracle.com/pls/topic/lookup?ctx=javase15&id=GUID-8296D8E8-2B93-4B9A-856E-0A65AF9B8C66 (SerialFilterChecker.java:45) 
DEBUG 07-04 09:58:23,705 Cache Hit Ratio [com.liaoii.mybatis.mapper.CacheMapper]: 0.5 (LoggingCache.java:60) 
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
```

### 8.4 二级缓存相关配置

在mapper配置文件中添加的cache标签可以设置一些属性：

eviction属性：缓存回收策略

​		LRU（Least Recently Used）：最近最少使用，移除最长时间不被使用的对象，默认为LRU

​		FIFO（First in First out）：先进先出，按对象进入缓存的顺序来移除它们

​		SOFT：软引用，移除基于垃圾回收器状态和软引用规则的对象

​		WEAK：弱引用，更积极地移除基于垃圾收集器状态和弱引用规则的对象

flushInterval属性：刷新间隔，单位毫秒

​		默认情况下不设置，也就是没有刷新间隔，缓存仅仅在调用增删改时刷新

size属性：引用数目，正整数

​		代表缓存最多可以存储多少个对象，太大容易导致内存溢出

readOnly属性：只读

​		true：只读缓存，会给所有调用者返回对象的相同实例，因此这些对象不能被修改；这提供了很重要的性能优势

​		false：读写缓存，会返回缓存对象的拷贝（通过序列化），使用读写缓存的性能会慢一些，但是也会安全一些；默认为false

### 8.5 MyBatis缓存查询的顺序

由于二级缓存的范围比一级缓存的范围大，所以在查询时先查询二级缓存；如果二级缓存没有命中再查询一级缓存；如果一级缓存也没有命中则查询数据库

SqlSession关闭之后，一级缓存中的数据会写入二级缓存

### 8.6 整合第三方缓存EHCache

可以使用第三方缓存来代替MyBatis的二级缓存，而MyBatis的一级缓存无法被代替

添加依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis.caches</groupId>
        <artifactId>mybatis-ehcache</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.10</version>
    </dependency>
</dependencies>
```

创建ehcache.xml：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\mybatis\ehcache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```

设置二级缓存的类型：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.CacheMapper">

    <cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>

    <select id="getEmpByEid" resultType="Emp">
        select * from t_emp where eid = #{eid}
    </select>
    
    <insert id="insertEmp">
        insert into t_emp values (null, #{empName}, #{age}, #{sex}, #{email}, null)
    </insert>
</mapper>
```

创建logback.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger] [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.atguigu.crowd.mapper" level="DEBUG"/>
</configuration>
```

CacheTest：

```java
public class CacheTest {
    @Test
    public void test04() {
        try {
            InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
            SqlSession session1 = factory.openSession(true);
            CacheMapper mapper1 = session1.getMapper(CacheMapper.class);
            Emp emp1 = mapper1.getEmpByEid(1);
            System.out.println(emp1);
            session1.close();

            SqlSession session2 = factory.openSession(true);
            CacheMapper mapper2 = session2.getMapper(CacheMapper.class);
            Emp emp2 = mapper2.getEmpByEid(1);
            System.out.println(emp2);
            session2.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

结果：

```xml
[14:49:04.334] [DEBUG] [main] [com.liaoii.mybatis.mapper.CacheMapper.getEmpByEid] 
	[==>  Preparing: select * from t_emp where eid = ?]
[14:49:04.363] [DEBUG] [main] [com.liaoii.mybatis.mapper.CacheMapper.getEmpByEid] 
	[==> Parameters: 1(Integer)]
[14:49:04.380] [DEBUG] [main] [com.liaoii.mybatis.mapper.CacheMapper.getEmpByEid] 
	[<==      Total: 1]
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
[14:49:04.382] [DEBUG] [main] [net.sf.ehcache.store.disk.Segment] 
    [put added 0 on heap]
[14:49:04.383] [DEBUG] [main] [org.apache.ibatis.transaction.jdbc.JdbcTransaction] 
	[Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@54a7079e]]
[14:49:04.383] [DEBUG] [main] [org.apache.ibatis.datasource.pooled.PooledDataSource] 
    [Returned connection 1420232606 to pool.]
[14:49:04.384] [DEBUG] [main] [com.liaoii.mybatis.mapper.CacheMapper] 
    [Cache Hit Ratio [com.liaoii.mybatis.mapper.CacheMapper]: 0.5]
Emp{eid=1, empName='张珊', age=18, sex='女', email='123@qq.com', dept=null}
```

EHCache配置文件说明：

| 属性名                          | 是否必须 | 作用                                                         |
| ------------------------------- | -------- | ------------------------------------------------------------ |
| maxElementsInMemory             | 是       | 在内存中缓存的element的最大数目                              |
| maxElementsOnDisk               | 是       | 在磁盘上缓存的element的最大数目，若是0表示无 穷大            |
| eternal                         | 是       | 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds、timeToLiveSeconds判断 |
| overflowToDisk                  | 是       | 设定当内存缓存溢出的时候是否将过期的element缓存到磁盘上      |
| timeToIdleSeconds               | 否       | 当缓存在EhCache中的数据前后两次访问的时间超过timeToIdleSeconds的属性取值时，这些数据便会删除，默认值是0，也就是可闲置时间无穷大 |
| timeToLiveSeconds               | 否       | 缓存element的有效生命期，默认是0，也就是element存活时间无穷大 |
| diskSpoolBufferSizeMB           | 否       | DiskStore（磁盘缓存）的缓存区大小，默认是30MB，每个Cache都应该有自己的一个缓冲区 |
| diskPersistent                  | 否       | 在VM重启的时候是否启用磁盘保存EhCache中的数据，默认是false   |
| diskExpiryThreadIntervalSeconds | 否       | 磁盘缓存的清理线程运行间隔，默认是120秒，每个120秒，相应的线程会进行一次EhCache中数据的清理工作 |
| memoryStoreEvictionPolicy       | 否       | 当内存缓存达到最大，有新的element加入的时候，移除缓存中element的策略。 默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出） |


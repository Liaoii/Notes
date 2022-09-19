## 3. MyBatis获取参数值的两种方式

### 3.1 在IDEA中设置核心配置文件的模板

File --> Settings... --> Editor --> File and Code Templates --> Files --> +

​		Name: mybaties-config

​		Extension: xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <properties resource="jdbc.properties"/>
    
    <typeAliases>
        <package name=""/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    
    <mappers>
        <package name=""/>
    </mappers>
    
</configuration>
```

### 3.2 在IDEA中设置映射文件的模板

File --> Settings... --> Editor --> File and Code Templates --> Files --> +

​		Name: mybaties-mapper

​		Extension: xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">

</mapper>
```

### 3.3 封装SqlSessionUtils工具类

```java
public class SqlSessionUtils {
    public static SqlSession getSqlSession() {
        SqlSession session = null;
        try {
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
            SqlSessionFactory factory = builder.build(is);
            session = factory.openSession(true);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return session;
    }
}
```

### 3.4 ${}与#{}的区别

**${}**的本质是字符串拼接

**#{}**的本质是占位符赋值

### 3.5 Mapper接口方法的参数为单个的字面量类型

**ParameterMapper**：

```java
public interface ParameterMapper {
    User getUserByUserName(String userName);
}
```

**ParameterMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.ParameterMapper">
    <select id="getUserByUserName" resultType="User">
        <!--select * from t_user where username = #{username}-->
        select * from t_user where username = #{any}
        <!--select * from t_user where username = '${username}'-->
    </select>
</mapper>
```

**ParameterMapperTest**：

```java
public class ParameterMapperTest {

    @Test
    public void test02() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
        User user = mapper.getUserByUserName("Lily");
        System.out.println(user);
    }
}
```

**总结**：可以通过\${}和#{}以任意的名称获取参数值，但是需要注意\${}的单引号问题。

### 3.6 Mapper接口方法的参数为多个时

**ParameterMapper**：

```java
public interface ParameterMapper {
    User checkLogin(String username, String password);
}
```

**ParameterMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.ParameterMapper">
    <select id="checkLogin" resultType="User">
        <!--select * from t_user where username = #{arg0} and password = #{arg1}-->
        <!--select * from t_user where username = #{param1} and password = #{param2}-->
        <!--select * from t_user where username = #{arg0} and password = #{param2}-->
        select * from t_user where username = '${arg0}' and password = '${param2}'
    </select>
</mapper>
```

**ParameterMapperTest**：

```java
public class ParameterMapperTest {
    @Test
    public void test03() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
        User user = mapper.checkLogin("Lily","123456");
        System.out.println(user);
    }
}
```

**总结**：

当Mapper接口方法的参数为多个时，MyBatis会将这些参数放在一个Map集合中，以两种方式进行存储：以arg0、arg1...为键，以参数为值；以param1、param2...为键，以参数为值。因此只需要通过#{}和${}以键的方式访问值即可，但是需要注意\${}的单引号问题。

### 3.7 使用Map作为Mapper接口方法的传入值

**ParameterMapper**：

```java
public interface ParameterMapper {
    User checkLoginByMap(Map<String, Object> map);
}
```

**ParameterMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.ParameterMapper">
    <select id="checkLoginByMap" resultType="User">
        <!--select * from t_user where username = #{username} and password = #{password}-->
        select * from t_user where username = '${username}' and password = '${password}'
    </select>
</mapper>
```

**ParameterMapperTest**：

```java
public class ParameterMapperTest {
    @Test
    public void test04() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
        Map<String, Object> map = new HashMap<>();
        map.put("username", "Lily");
        map.put("password", "123456");
        User user = mapper.checkLoginByMap(map);
        System.out.println(user);
    }
}
```

**总结**：使用Map中自定义的键值来引用相应的值

### 3.8 Mapper接口方法的参数是实体类类型

**ParameterMapper**：

```java
public interface ParameterMapper {
    int insertUser(User user);
}
```

**ParameterMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.ParameterMapper">
    <insert id="insertUser">
        insert into t_user values (null, #{username}, #{password}, #{age}, #{sex}, #{email})
    </insert>
</mapper>
```

**ParameterMapperTest**：

```java
public class ParameterMapperTest {
    @Test
    public void test05() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
        User user = new User(null, "李思", "123456", 22, "女", "1314@163.com");
        int result = mapper.insertUser(user);
        System.out.println(result);
    }
}
```

**总结**：使用对象的属性名来引用参数，其原理是调用对象对应属性的get方法，不一定非要有该属性

### 3.9 使用@Param注解命名参数

**ParameterMapper**：

```
public interface ParameterMapper {
    User checkLoginByParam(@Param("username") String username, @Param("password") String password);
}
```

**ParameterMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.ParameterMapper">
    <select id="checkLoginByParam" resultType="com.liaoii.mybatis.entity.User">
        select * from t_user where username = #{username} and password = #{password}
    </select>
</mapper>
```

**ParameterMapperTest**：

```java
public class ParameterMapperTest {
    @Test
    public void test06() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
        User user = mapper.checkLoginByParam("Lily", "123456");
        System.out.println(user);
    }
}
```

**总结**：此时MyBatis会将这些参数放在一个Map集合中，以两种方式进行存储：以@Param注解的值为键，以参数为值；以param1、param2...为键，以参数为值

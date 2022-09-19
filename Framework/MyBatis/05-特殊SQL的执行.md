## 5. 特殊SQL的执行

### 5.1 模糊查询

**SQLMapper**：

```java
public interface SQLMapper {
    List<User> getUserByLike(@Param("username") String username);
}
```

**SQLMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SQLMapper">
    <select id="getUserByLike" resultType="User">
        <!--select * from t_user where username like '%${username}%'-->
        <!--select * from t_user where username like concat('%',#{username},'%')-->
        select * from t_user where username like "%"#{username}"%"
    </select>
</mapper>
```

**SQLMapperTest**：

```java
public class SQLMapperTest {
    @Test
    public void test01() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        SQLMapper mapper = session.getMapper(SQLMapper.class);
        List<User> list = mapper.getUserByLike("a");
        list.forEach(System.out::println);
    }
}
```

**总结**：实现模糊查询有三种方式，但无法使用 '%#{username}%'

### 5.2 批量删除

**SQLMapper**：

```java
public interface SQLMapper {
    int deleteMore(@Param("ids") String ids);
}
```

**SQLMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SQLMapper">
    <delete id="deleteMore">
        delete from t_user where id in (${ids})
    </delete>
</mapper>
```

**SQLMapperTest**：

```java
public class SQLMapperTest {
    @Test
    public void test02() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        SQLMapper mapper = session.getMapper(SQLMapper.class);
        int result = mapper.deleteMore("1,2,3");
        System.out.println(result);
    }
}
```

**总结**：不能使用 (#{ids})

### 5.3 动态设置表名

**SQLMapper**：

```java
public interface SQLMapper {
    /**
     * 查询指定表中的数据
     */
    List<User> getUserByTableName(@Param("tableName") String tableName);
}
```

**SQLMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SQLMapper">
    <select id="getUserByTableName" resultType="User">
        select * from ${tableName}
    </select>
</mapper>
```

**SQLMapperTest**：

```java
public class SQLMapperTest {
    @Test
    public void test03() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        SQLMapper mapper = session.getMapper(SQLMapper.class);
        List<User> list = mapper.getUserByTableName("t_user");
        list.forEach(System.out::println);
    }
}
```

### 5.4 添加功能获取自增的主键

**SQLMapper**：

```java
public interface SQLMapper {
    int insertUser(User user);
}
```

**SQLMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SQLMapper">
    <insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        insert into t_user values (null, #{username}, #{password}, #{age}, #{sex}, #{email})
    </insert>
</mapper>
```

**SQLMapperTest**：

```java
public class SQLMapperTest {
    @Test
    public void test04() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        SQLMapper mapper = session.getMapper(SQLMapper.class);
        User user = new User(null, "Liaoii", "123", 22, "男", "1314@163.com");
        int result = mapper.insertUser(user);
        System.out.println(result);
        System.out.println(user);
    }
}
```

**总结**：

useGeneratedKeys：设置当前标签中的SQL使用了自增的主键

keyProperty：将自增的主键的值赋值给传输到映射文件中参数的某个属性

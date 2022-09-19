## 4. MyBatis的各种查询功能

### 4.1 查询一个实体类对象

**SelectMapper**：

```java
public interface SelectMapper {
    User getUserById(@Param("id") Integer id);
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getUserById" resultType="User">
        select * from t_user where id = #{id}
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {

    @Test
    public void test01() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        User user = mapper.getUserById(2);
        System.out.println(user);
    }
}
```

**总结**：

若查询出的数据只有一条：

​		可以通过实体类对象或者集合接收

若查询出的数据有多条：

​		可以通过集合接收

​		一定不能通过实体类对象接收，此时会抛异常TooManyResultException

### 4.2 查询一个集合

**SelectMapper**：

```java
public interface SelectMapper {
    List<User> getAllUser();
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getAllUser" resultType="User">
        select * from t_user
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {
    @Test
    public void test02() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        List<User> users = mapper.getAllUser();
        users.forEach(System.out::println);
    }
}
```

### 4.3 查询单行单列的数据

**SelectMapper**：

```java
public interface SelectMapper {
    Integer getCount();
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getCount" resultType="int">
        select count(*) from t_user
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {
    @Test
    public void test03() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        int count = mapper.getCount();
        System.out.println(count);
    }
}
```

**总结**：MyBatis中设置了默认的类型别名

​		java.lang.Integer --> int、integer

​		int --> \_int、\_integer

​		Map --> map

### 4.4 查询一条数据到Map中

**SelectMapper**：

```java
public interface SelectMapper {
    Map<String, Object> getUserByIdToMap(@Param("id") Integer id);
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getUserByIdToMap" resultType="map">
        select * from t_user where id = #{id}
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {
    @Test
    public void test04() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        Map<String,Object> map = mapper.getUserByIdToMap(2);
        System.out.println(map);
    }
}
```

**总结**：若查询出的数据只有一条，可以使用Map集合进行接收

### 4.5 查询多条数据到Map集合

**SelectMapper**：

```java
public interface SelectMapper {
    List<Map<String, Object>> getAllUserToMap();
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getAllUserToMap" resultType="map">
        select * from t_user
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {
    @Test
    public void test05() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        List<Map<String, Object>> list = mapper.getAllUserToMap();
        list.forEach(System.out::println);
    }
}
```

**总结**：若查询出的数据有多条，可以通过map类型的集合进行接收，不能通过单个map集合进行接收，此时会抛异常TooManyResultException

### 4.6 @MapKey注解的使用

**SelectMapper**：

```java
public interface SelectMapper {
    @MapKey("id")
    Map<String, Object> getAllUserToMapKey();
}
```

**SelectMapper.xml**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.mybatis.mapper.SelectMapper">
    <select id="getAllUserToMapKey" resultType="map">
        select * from t_user
    </select>
</mapper>
```

**SelectMapperTest**：

```java
public class SelectMapperTest {
    @Test
    public void test06() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        Map<String, Object> map = mapper.getAllUserToMapKey();
        System.out.println(map);
    }
}
```


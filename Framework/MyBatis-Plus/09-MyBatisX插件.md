## 9. MyBatisX插件

在IDEA中安装MyBatisX插件：File --> Setting... --> Plugins --> MyBatisX

在IDEA中通过Database连接数据库

在数据库表上点击右键，选择MybatisX-Generator：

​		model path：F:/Applications/ideaIU-2021.3.1.win/workspace/MyBatis-Plus/MyBatisPlus03

​		base package：com.liaoii.mybatisplus

​		encoding：UTF-8

​		base path：src/main/java

​		ignore table prefix：t_

​		relative package：entity

​		annotation：Mybatis-Plus 3

​		options：Comment、Lombok

​		template：mybatis-plus3

生成结果：

**User**

```java
@TableName(value ="t_user")
@Data
public class User implements Serializable {

    @TableId(type = IdType.AUTO)
    private Long uid;

    private String userName;

    private Integer age;

    private String email;

    private Integer isDeleted;

    private Integer sex;

    @TableField(exist = false)
    private static final long serialVersionUID = 1L;
}
```

**UserMapper**

```java
public interface UserMapper extends BaseMapper<User> {

}
```

**UserService**

```java
public interface UserService extends IService<User> {

}
```

**UserServiceImpl**

```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {

}
```

**UserMapper.xml**

```xml
<mapper namespace="com.liaoii.mybatisplus.mapper.UserMapper">

    <resultMap id="BaseResultMap" type="com.liaoii.mybatisplus.entity.User">
            <id property="uid" column="uid" jdbcType="BIGINT"/>
            <result property="userName" column="user_name" jdbcType="VARCHAR"/>
            <result property="age" column="age" jdbcType="INTEGER"/>
            <result property="email" column="email" jdbcType="VARCHAR"/>
            <result property="isDeleted" column="is_deleted" jdbcType="INTEGER"/>
            <result property="sex" column="sex" jdbcType="INTEGER"/>
    </resultMap>

    <sql id="Base_Column_List">
        uid,user_name,age,
        email,is_deleted,sex
    </sql>
</mapper>
```
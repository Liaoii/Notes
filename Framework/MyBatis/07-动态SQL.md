## 7. 动态SQL

### 7.1 if

**DynamicSQLMapper**：

```java
public interface DynamicSQLMapper {
    List<Emp> getEmpByCondition(Emp emp);
}
```

**DynamicSQLMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp where 1 = 1
        <if test="empName != null and empName != ''">
            and emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="sex != null and sex != ''">
            and sex = #{sex}
        </if>
        <if test="email != null and email != ''">
            and email = #{email}
        </if>
    </select>
</mapper>
```

**DynamicSQLTest**：

```java
public class DynamicSQLTest {

    @Test
    public void test01() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        DynamicSQLMapper mapper = session.getMapper(DynamicSQLMapper.class);
        Emp emp = new Emp();
        emp.setEmpName("张珊");
        emp.setAge(18);
        List<Emp> list = mapper.getEmpByCondition(emp);
        list.forEach(System.out::println);
    }
}
```

if可以根据标签中test属性所对应的表达式决定标签中的内容是否需要拼接到SQL中

### 7.2 where

当where标签中有内容时，会自动生成where关键字，并且将内容前多余的and或or去掉

当where标签中没有内容时，此时where标签没有任何效果

注意：where标签不能将其中内容后面多余的and或or去掉

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp
        <where>
            <if test="empName != null and empName != ''">
                emp_name = #{empName}
            </if>
            <if test="age != null and age != ''">
                and age = #{age}
            </if>
            <if test="sex != null and sex != ''">
                and sex = #{sex}
            </if>
            <if test="email != null and email != ''">
                and email = #{email}
            </if>
        </where>
    </select>
</mapper>
```

### 7.3 trim

若标签中有内容时：

prefix|suffix：在trim标签中内容前面或后面添加指定内容

suffixOverrides|prefixOverrides：在trim标签中内容前面或后面去掉指定内容

若标签中没有内容时，trim标签没有任何效果

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp
        <trim prefix="where" suffixOverrides="and|or">
            <if test="empName != null and empName != ''">
                emp_name = #{empName} and
            </if>
            <if test="age != null and age != ''">
                age = #{age} and
            </if>
            <if test="sex != null and sex != ''">
                sex = #{sex} and
            </if>
            <if test="email != null and email != ''">
                email = #{email}
            </if>
        </trim>
    </select>
</mapper>
```

### 7.4 choose、when、otherwise

choose、when、otherwise相当于if...else if...else

when至少要有一个，otherwise最多只能有一个

**DynamicSQLMapper**：

```java
public interface DynamicSQLMapper {
    List<Emp> getEmpByChoose(Emp emp);
}
```

**DynamicSQLMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <select id="getEmpByChoose" resultType="Emp">
        select * from t_emp
        <where>
            <choose>
                <when test="empName != null and empName != ''">
                    emp_name = #{empName}
                </when>
                <when test="age != null and age != ''">
                    age = #{age}
                </when>
                <when test="sex != null and sex != ''">
                    sex = #{sex}
                </when>
                <when test="email != null and email != ''">
                    email = #{email}
                </when>
                <otherwise>
                    eid = 1
                </otherwise>
            </choose>
        </where>
    </select>
</mapper>
```

**DynamicSQLTest**：

```java
public class DynamicSQLTest {
    @Test
    public void test02() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        DynamicSQLMapper mapper = session.getMapper(DynamicSQLMapper.class);
        Emp emp = new Emp();
        emp.setEmpName("张珊");
        emp.setAge(18);
        //emp.setSex("男");
        //emp.setEmail("123@qq.com");
        List<Emp> list = mapper.getEmpByChoose(emp);
        list.forEach(System.out::println);
    }
}
```

### 7.5 foreach-批量删除

**DynamicSQLMapper**：

```java
public interface DynamicSQLMapper {
    int deleteMoreByArray(@Param("eids") Integer[] eids);
}
```

**DynamicSQLMapper.xml**：

第一种方法：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <delete id="deleteMoreByArray">
        delete from t_emp where eid in
        <foreach collection="eids" item="eid" separator="," open="(" close=")">
            #{eid}
        </foreach>
    </delete>
</mapper>
```

第二种方法：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <delete id="deleteMoreByArray">
        delete from t_emp where
        <foreach collection="eids" item="eid" separator="or">
            eid = #{eid}
        </foreach>
    </delete>
</mapper>
```

**DynamicSQLTest**：

```java
public class DynamicSQLTest {
    @Test
    public void test03() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        DynamicSQLMapper mapper = session.getMapper(DynamicSQLMapper.class);
        int result = mapper.deleteMoreByArray(new Integer[]{6, 7, 8});
        System.out.println(result);
    }
}
```

### 7.6 foreach-批量插入

**DynamicSQLMapper**：

```java
public interface DynamicSQLMapper {
    List<Emp> getEmpByCondition(Emp emp);
    List<Emp> getEmpByChoose(Emp emp);
    int deleteMoreByArray(@Param("eids") Integer[] eids);
    int insertMoreByList(@Param("emps") List<Emp> emps);
}
```

**DynamicSQLMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <insert id="insertMoreByList">
        insert into t_emp values
        <foreach collection="emps" item="emp" separator=",">
            (null, #{emp.empName}, #{emp.age}, #{emp.sex}, #{emp.email}, null)
        </foreach>
    </insert>
</mapper>
```

**DynamicSQLTest**：

```java
public class DynamicSQLTest {
    @Test
    public void test04() {
        SqlSession session = SqlSessionUtils.getSqlSession();
        DynamicSQLMapper mapper = session.getMapper(DynamicSQLMapper.class);
        Emp emp1 = new Emp(null, "aa", 18, "女", "aa@qq.com");
        Emp emp2 = new Emp(null, "bb", 18, "女", "bb@qq.com");
        Emp emp3 = new Emp(null, "cc", 18, "女", "cc@qq.com");
        List<Emp> emps = Arrays.asList(emp1, emp2, emp3);
        int result = mapper.insertMoreByList(emps);
        System.out.println(result);
    }
}
```

foreach总结：

collection：设置需要循环的数组或集合

item：表示数组或集合中的每一个数据

separator：循环体之间的分隔符

open：foreach标签所循环的所有内容的开始符

close：foreach标签所循环的所有内容的结束符

### 7.7 SQL标签

**DynamicSQLMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DynamicSQLMapper">
    <sql id="empColumns">eid,emp_name,age,sex,email</sql>

    <select id="getEmpByCondition" resultType="Emp">
        select
        <include refid="empColumns"></include>
        from t_emp
        <trim prefix="where" suffixOverrides="and|or">
            <if test="empName != null and empName != ''">
                emp_name = #{empName} and
            </if>
            <if test="age != null and age != ''">
                age = #{age} and
            </if>
            <if test="sex != null and sex != ''">
                sex = #{sex} and
            </if>
            <if test="email != null and email != ''">
                email = #{email}
            </if>
        </trim>
    </select>
</mapper>
```

\<sql\>标签用于设置SQL片段，\<include\>用于引用SQL片段
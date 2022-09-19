## 6. 自定义映射resultMap

### 6.1 resultMap处理字段和属性的映射关系

**EmpMapper**：

```java
public interface EmpMapper {
    List<Emp> getAllEmp();
}
```

**EmpMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getAllEmp" resultType="Emp">
        select * from t_emp
    </select>
</mapper>
```

**ResultMapTest**：

```java
public class ResultMapTest {

    @Test
    public void test01() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        List<Emp> list = mapper.getAllEmp();
        list.forEach(System.out::println);
    }
}
```

**结果**：

```
Emp{eid=1, empName='null', age=18, sex='女', email='123@qq.com'}
Emp{eid=2, empName='null', age=21, sex='男', email='123@qq.com'}
Emp{eid=3, empName='null', age=25, sex='女', email='123@qq.com'}
Emp{eid=4, empName='null', age=22, sex='男', email='123@qq.com'}
Emp{eid=5, empName='null', age=23, sex='女', email='123@qq.com'}
```

由于Emp中empName字段与t_emp表中的emp_name无法匹配，所以在查询时无法为其赋值。

**第一种解决方法**：为字段起别名，保持和属性名的一致

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getAllEmp" resultType="Emp">
        <!--select * from t_emp-->
        select eid, emp_name empName, age, sex, email from t_emp
    </select>
</mapper>
```

**第二种解决方法**：在全局配置文件中将mapUnderscoreToCamelCase设置为true，意思是将下划线自动映射成驼峰命名

```xml
<configuration>

    <properties resource="jdbc.properties"/>

    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    
    <typeAliases>
        <package name="com.liaoii.mybatis.entity"/>
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
        <package name="com.liaoii.mybatis.mapper"/>
    </mappers>

</configuration>
```

**第三种解决方法**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getAllEmp" resultMap="empResultMap">
        select * from t_emp
    </select>

    <resultMap id="empResultMap" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
    </resultMap>
</mapper>
```

总结：

resultMap：设置自定义映射关系

​		id：唯一标识，不能重复

​		type：设置映射关系中的实体类类型

id：设置主键的映射关系

result：设置普通字段的映射关系

​		property：设置映射关系中的属性名，必须是type属性所设置的实体类类型中的属性名

​		column：设置映射关系中的字段名，必须是SQL语句查询出的字段名

### 6.2多对一的映射关系

**第一种方式：**通过级联属性赋值

修改实体类：

```java
public class Emp {
    private Integer eid;
    private String empName;
    private Integer age;
    private String sex;
    private String email;
    private Dept dept;

    public Emp() { }

    public Emp(Integer eid, String empName, Integer age, String sex, String email) {
        this.eid = eid;
        this.empName = empName;
        this.age = age;
        this.sex = sex;
        this.email = email;
    }

    public Integer getEid() { return eid; }
    public void setEid(Integer eid) { this.eid = eid; }
    public String getEmpName() { return empName; }
    public void setEmpName(String empName) { this.empName = empName; }
    public Integer getAge() { return age; }
    public void setAge(Integer age) { this.age = age; }
    public String getSex() { return sex; }
    public void setSex(String sex) { this.sex = sex; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public Dept getDept() { return dept; }
    public void setDept(Dept dept) { this.dept = dept; }

    @Override
    public String toString() {
        return "Emp{" +
                "eid=" + eid +
                ", empName='" + empName + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                ", email='" + email + '\'' +
                ", dept=" + dept +
                '}';
    }
}
```

**EmpMapper**：

```java
public interface EmpMapper {
    Emp getEmpAndDept(@Param("eid") Integer eid);
}
```

**EmpMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getEmpAndDept" resultMap="empAndDeptResultMap_1">
        select * from t_emp left join t_dept on t_emp.did = t_dept.did where t_emp.eid = #{eid}
    </select>

    <resultMap id="empAndDeptResultMap_1" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
        <result property="dept.did" column="did"></result>
        <result property="dept.deptName" column="dept_name"></result>
    </resultMap>

</mapper>
```

**ResultMapTest**：

```java
public class ResultMapTest {
    @Test
    public void test02() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        Emp emp = mapper.getEmpAndDept(1);
        System.out.println(emp);
    }
}
```

**第二种方式：**使用association标签

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getEmpAndDept" resultMap="empAndDeptResultMap_2">
        select * from t_emp left join t_dept on t_emp.did = t_dept.did where t_emp.eid = #{eid}
    </select>

    <resultMap id="empAndDeptResultMap_2" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
        <association property="dept" javaType="Dept">
            <id property="did" column="did"></id>
            <result property="deptName" column="dept_name"></result>
        </association>
    </resultMap>
</mapper>
```

association：处理多对一的映射关系

​		property：需要处理的映射关系的属性名

​		javaType：该属性的类型

**第三种方式：**使用分步查询

**EmpMapper**：

```java
public interface EmpMapper {
    Emp getEmpAndDeptByStepOne(@Param("eid") Integer eid);
}
```

**DeptMapper**：

```java
public interface DeptMapper {
    Dept getEmpAndDeptByStepTwo(@Param("did") Integer did);
}
```

**EmpMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
        select * from t_emp where eid = #{eid}
    </select>

    <resultMap id="empAndDeptByStepResultMap" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
        <association property="dept"
                     select="com.liaoii.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                     column="did"></association>
    </resultMap>
</mapper>
```

**DeptMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DeptMapper">
    <select id="getEmpAndDeptByStepTwo" resultType="Dept">
        select * from t_dept where did = #{did}
    </select>
</mapper>
```

**ResultMapTest**：

```java
public class ResultMapTest {
    @Test
    public void test03() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        Emp emp = mapper.getEmpAndDeptByStepOne(4);
        System.out.println(emp);
    }
}
```

**总结**：

​		select：设置分步查询的SQL的唯一标识，namespace.SQLId或mapper接口的全类名.方法名

​		column：设置分步查询的条件

### 6.3 延迟加载

分步查询的优点是可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息：

lazyLoadingEnabled：延迟加载的全局开关，当开启时，所有关联对象都会延迟加载

aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性，否则每个属性会按需加载

此时就可以实现按需加载，获取的数据是什么，就只会执行相应的SQL。此时可通过association和collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy(延迟加载)|eager(立即加载)"

如果lazyLoadingEnabled开关没有打开，则fetchType属性将无效

**mybatis-config.xml**：

```xml
<configuration>
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>
    </settings>
</configuration>
```

**ResultMapTest**：

```
public class ResultMapTest {
    @Test
    public void test04() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        Emp emp = mapper.getEmpAndDeptByStepOne(4);
        System.out.println(emp.getEmpName());
        System.out.println("----------");
        System.out.println(emp);
    }
}
```

**结果**：

```
DEBUG 07-02 13:35:41,526 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:35:41,562 ==> Parameters: 4(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:35:41,649 <==      Total: 1 (BaseJdbcLogger.java:137) 
赵留
----------
DEBUG 07-02 13:35:41,650 ==>  Preparing: select * from t_dept where did = ? (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:35:41,650 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:35:41,652 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=4, empName='赵留', age=22, sex='男', email='123@qq.com', dept=Dept{did=1, deptName='A'}}
```

使用fetchType关闭延迟加载：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
        select * from t_emp where eid = #{eid}
    </select>

    <resultMap id="empAndDeptByStepResultMap" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
        <association property="dept"
                     select="com.liaoii.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                     column="did"
                     fetchType="eager"></association>
    </resultMap>
</mapper>
```

**结果**：

```
DEBUG 07-02 13:39:48,673 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:39:48,710 ==> Parameters: 4(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:39:48,730 ====>  Preparing: select * from t_dept where did = ? (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:39:48,730 ====> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:39:48,732 <====      Total: 1 (BaseJdbcLogger.java:137) 
DEBUG 07-02 13:39:48,733 <==      Total: 1 (BaseJdbcLogger.java:137) 
赵留
----------
Emp{eid=4, empName='赵留', age=22, sex='男', email='123@qq.com', dept=Dept{did=1, deptName='A'}}
```

### 6.4 一对多关系

**第一种方式：**使用collection标签

修改实体类：

```java
public class Dept {
    private Integer did;
    private String deptName;
    private List<Emp> emps;

    public Dept() { }

    public Dept(Integer did, String deptName) {
        this.did = did;
        this.deptName = deptName;
    }

    public Integer getDid() { return did; }
    public void setDid(Integer did) { this.did = did; }
    public String getDeptName() { return deptName; }
    public void setDeptName(String deptName) { this.deptName = deptName; }
    public List<Emp> getEmps() { return emps; }
    public void setEmps(List<Emp> emps) { this.emps = emps; }

    @Override
    public String toString() {
        return "Dept{" +
                "did=" + did +
                ", deptName='" + deptName + '\'' +
                ", emps=" + emps +
                '}';
    }
}
```

**DeptMapper**：

```java
public interface DeptMapper {
    Dept getDeptAndEmp(@Param("did") Integer did);
}
```

**DeptMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DeptMapper">
    <select id="getDeptAndEmp" resultMap="deptAndEmpResultMap">
        select * from t_dept left join t_emp on t_dept.did = t_emp.did where t_dept.did = #{did}
    </select>

    <resultMap id="deptAndEmpResultMap" type="Dept">
        <id property="did" column="did"></id>
        <result property="deptName" column="dept_name"></result>
        <collection property="emps" ofType="Emp">
            <id property="eid" column="eid"></id>
            <result property="empName" column="emp_name"></result>
            <result property="age" column="age"></result>
            <result property="sex" column="sex"></result>
            <result property="email" column="email"></result>
        </collection>
    </resultMap>
</mapper>
```

**ResultMapTest**：

```java
public class ResultMapTest {
    @Test
    public void test05() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
        Dept dept = mapper.getDeptAndEmp(1);
        System.out.println(dept);
    }
}
```

**第二种方式：**分步查询

**DeptMapper**：

```java
public interface DeptMapper {
    Dept getDeptAndEmpByStepOne(@Param("did") Integer did);
}
```

**EmpMapper**：

```java
public interface EmpMapper {
    List<Emp> getDeptAndEmpByStepTwo(@Param("did") Integer did);
}
```

**DeptMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.DeptMapper">
    <resultMap id="deptAndEmpByStepResultMap" type="Dept">
        <id property="did" column="did"></id>
        <result property="deptName" column="dept_name"></result>
        <collection property="emps"
                    select="com.liaoii.mybatis.mapper.EmpMapper.getDeptAndEmpByStepTwo"
                    column="did"></collection>
    </resultMap>

    <select id="getDeptAndEmpByStepOne" resultMap="deptAndEmpByStepResultMap">
        select * from t_dept where did = #{did}
    </select>
</mapper>
```

**EmpMapper.xml**：

```xml
<mapper namespace="com.liaoii.mybatis.mapper.EmpMapper">
    <resultMap id="empAndDeptByStepResultMap" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
        <association property="dept"
                     select="com.liaoii.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                     column="did"
                     fetchType="eager"></association>
    </resultMap>

    <select id="getDeptAndEmpByStepTwo" resultType="Emp">
        select * from t_emp where did = #{did}
    </select>
</mapper>
```

**ResultMapTest**：

```java
public class ResultMapTest {
    @Test
    public void test06() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
        Dept dept = mapper.getDeptAndEmpByStepOne(1);
        System.out.println(dept.getDeptName());
    }
}
```
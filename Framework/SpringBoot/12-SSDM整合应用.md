## 12. SSDM整合应用

导入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

全局配置：

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
    username: root
    password: 123456
  redis:
    host: localhost
    port: 6379
    password: 123456
  cache:
    type: REDIS
    cache-names:
      - realTimeCache
```

**index.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="some/register" method="post">
        姓名：<input type="text" name="name"> <br/>
        年龄：<input type="text" name="age"> <br/>
        <input type="submit" value="注册">
    </form>
    <hr>
    <form action="some/find" method="post">
        年龄上限：<input type="text" name="age"> <br>
        <input type="submit" value="查询">
    </form>
    <hr>
    <a href="some/count">查询学生总人数</a>
</body>
</html>
```

**SomeController**

```java
@Controller
@RequestMapping("/some")
public class SomeController {

    @Autowired
    private StudentService studentService;

    @PostMapping("/register")
    public String registerHandler(Student student, Model model) {
        model.addAttribute("student", student);
        studentService.addStudent(student);
        return "/jsp/welcome.jsp";
    }

    @PostMapping("/find")
    @ResponseBody
    public List<Student> findHandler(Integer age) {
        return studentService.findStudentBelowAge(age);
    }

    @GetMapping("/count")
    @ResponseBody
    public Integer countHandler() {
        return studentService.countStudent();
    }
}
```

**StudentDao**

```java
@Mapper
public interface StudentDao {
    int insertStudent(Student student);

    List<Student> selectStudentsBelowAge(Integer age);

    Integer selectStudentCount();
}
```

**StudentDao.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liaoii.springboot.dao.StudentDao">
    <insert id="insertStudent">
        insert into student(name, age)
        values (#{name}, #{age})
    </insert>

    <select id="selectStudentsBelowAge" resultType="Student">
        select id, name, age from student where age &lt;= #{age}
    </select>

    <select id="selectStudentCount" resultType="integer">
        select count(*) from student
    </select>
</mapper>
```

**Student**

```java
@Data
public class Student implements Serializable {
    private Integer id;
    private String name;
    private Integer age;
}
```

**S11SsdmApplication**

```java
@EnableCaching
@SpringBootApplication
public class S11SsdmApplication {

    public static void main(String[] args) {
        SpringApplication.run(S11SsdmApplication.class, args);
    }

}
```

**Redis 在高并发情况下可能会存在哪些问题？**

缓存穿透：当从数据库中查询结果为 null 时有可能引发缓存穿透问题，其解决方案是为这些为 null 的结果赋予一个默认值。

缓存雪崩：当缓存中的某些缓存在同一很短的时段内几乎同时到期，此时就可能会引发缓存雪崩问题，其解决方案是提前规划好系统中所有缓存的到期时间。

热点缓存：当某一个缓存的有效期到达时其可能会引发热点缓存问题，其解决方案是双重检测锁机制。

**StudentService**

```java
public interface StudentService {
    int addStudent(Student student);

    List<Student> findStudentBelowAge(Integer age);

    Integer countStudent();
}
```

**StudentServiceImpl**

```java
@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentDao studentDao;

    @Autowired
    private RedisTemplate<Object, Object> redisTemplate;

    @CacheEvict(value = "realTimeCache", allEntries = true)
    @Override
    public int addStudent(Student student) {
        return studentDao.insertStudent(student);
    }

    @Cacheable(value = "realTimeCache", key = "'student_' + #age")
    @Override
    public List<Student> findStudentBelowAge(Integer age) {
        return studentDao.selectStudentsBelowAge(age);
    }

    @Override
    public Integer countStudent() {
        BoundValueOperations<Object, Object> ops = redisTemplate.boundValueOps("count");
        Object count = ops.get();
        if (count == null) {
            synchronized (this) {
                count = ops.get();
                if (count == null) {
                    count = studentDao.selectStudentCount();
                    ops.set(count, 10, TimeUnit.SECONDS);
                }
            }
        }
        return (Integer) count;
    }
}
```

**双重检测锁存在的线程安全问题：**

**Student**

```java
@Data
@NoArgsConstructor
public class Student implements Serializable {
    private Integer id;
    private String name;
    private Integer age;

    public Student(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
}
```

当前代码存在线程安全问题：

```java
public class StudentFactory {
    private Student student;

    public Student getInstance() {
        if (student == null) {
            synchronized (this) {
                if (student == null) {
                    // 以下语句的底层实现由三步构成：
                    // 申请堆空间
                    // 使用对象的初始化数据初始化堆空间
                    // 将 student 引用指向堆空间
                    // 第三步可能先于第二步执行，导致对象没有被初始化
                    student = new Student("张珊", 18);
                }
            }
        }
        return student;
    }
}
```

解决方案：在方法签名上添加 synchronized ，使方法变为同步方法

```java
public class StudentFactory {
    private Student student;

    public synchronized Student getInstance() {
        if (student == null) {
            synchronized (this) {
                if (student == null) {
                    // 以下语句的底层实现由三步构成：
                    // 申请堆空间
                    // 使用对象的初始化数据初始化堆空间
                    // 将 student 引用指向堆空间
                    // 第三步可能先于第二步执行，导致对象没有被初始化
                    student = new Student("张珊", 18);
                }
            }
        }
        return student;
    }
}
```

解决方案：在存在线程安全问题的成员变量声明前添加 volatile 关键字

```java
public class StudentFactory {
    
    private volatile Student student;

    public Student getInstance() {
        if (student == null) {
            synchronized (this) {
                if (student == null) {
                    // 以下语句的底层实现由三步构成：
                    // 申请堆空间
                    // 使用对象的初始化数据初始化堆空间
                    // 将 student 引用指向堆空间
                    // 第三步可能先于第二步执行，导致对象没有被初始化
                    student = new Student("张珊", 18);
                }
            }
        }
        return student;
    }
}
```

解决方案：若存在线程安全的成员变量为 Integer、Long、Boolean 等，可以将它们定义为 AtomicXxx 类型
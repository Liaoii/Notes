## 9. MyBatis

导入依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.13</version>
    </dependency>
</dependencies>
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
}
```

**StudentService**

```java
public interface StudentService {
    int addStudent(Student student);
}
```

**StudentServiceImpl**

```java
@Service
public class StudentServiceImpl implements StudentService{

    @Autowired
    private StudentDao studentDao;

    @Override
    public int addStudent(Student student) {
        return studentDao.insertStudent(student);
    }
}
```

**StudentDao**

```java
@Mapper
public interface StudentDao {
    int insertStudent(Student student);
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
</mapper>
```

**将 dao 包注册为资源目录：**

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>

    <resources>
        <resource>
            <directory>src/main/webapp</directory>
            <targetPath>META-INF/resources</targetPath>
            <includes>
                <include>**/*.*</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

**新建数据库表：**

```sql
create table student
(
    id   int primary key auto_increment,
    name varchar(20),
    age  int(3)      
);
```

**修改配置文件：**

```yaml
server:
  port: 8888
mybatis:
  mapper-locations: classpath:com/liaoii/springboot/dao/*.xml
  type-aliases-package: com.liaoii.springboot.entity
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
    username: root
    password: 123456
```


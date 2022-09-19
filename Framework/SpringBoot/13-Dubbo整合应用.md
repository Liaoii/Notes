## 13. Dubbo整合应用

### 13.1 新建 S12-dubbo-commons 工程

**新建一个普通的 Maven 工程：**

Name：S12-dubbo-commons

GroupId：com.liaoii.springboot

ArtifactId：S12-dubbo-commons

**修改 pom 文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.liaoii.springboot</groupId>
    <artifactId>S12-dubbo-commons</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

</project>
```

新建实体类：

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

新建 Service 接口：

```java
public interface StudentService {
    int addStudent(Student student);

    List<Student> findStudentBelowAge(Integer age);

    Integer countStudent();
}
```

### 13.2 新建 S12-dubbo-provider 工程

pom 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.18.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.liaoii.springboot</groupId>
    <artifactId>S12-dubbo-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.liaoii.springboot</groupId>
            <artifactId>S12-dubbo-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.2</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.11</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.spring.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>

        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.10</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>

        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
    </build>
</project>
```

**StudentServiceImpl**

```java
@Service //com.alibaba.dubbo.config.annotation.Service;
@Component
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

**S12DubboProviderApplication**

```java
@EnableTransactionManagement
@EnableDubboConfiguration
@EnableCaching
@SpringBootApplication
public class S12DubboProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(S12DubboProviderApplication.class, args);
    }

}
```

**配置文件：**

```yaml
server:
  port: 8
mybatis:
  mapper-locations: classpath:com/liaoii/springboot/dao/*.xml
  type-aliases-package: com.liaoii.springboot.entity
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
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
  application:
    name: S12-dubbo-provider
  dubbo:
    register: zookeeper://192.168.178.111:2181
```

### 13.3 新建 S12-dubbo-consumer 工程

pom 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.18.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.liaoii.springboot</groupId>
    <artifactId>S12-dubbo-consumer</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.liaoii.springboot</groupId>
            <artifactId>S12-dubbo-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.spring.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </dependency>

        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.10</version>
        </dependency>
    </dependencies>

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
        </resources>
    </build>
</project>
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

    @Reference
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

**S12DubboConsumerApplication**

```java
@EnableDubboConfiguration
@EnableCaching
@SpringBootApplication()
public class S12DubboConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(S12DubboConsumerApplication.class, args);
    }

}
```

**application.yml**

```yaml
server:
  port: 8082

spring:
  application:
    name: S12-dubbo-consumer
  dubbo:
    registry: zookeeper://192.168.178.111:2181
```
## 17. thymeleaf

### 17.1 环境搭建

添加依赖：

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
    <artifactId>S16-thymeleaf</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

处理器：

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("welcome", "Hello Thymeleaf.");
        return "index";
    }
}
```

在 resources/templates 目录下新建 index.html：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:text="${welcome}">这里将要显示数据，但这些文字不显示。</p>
</body>
</html>
```

### 17.2 标准表达式

**变量表达式${}**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {
    private String name;
    private int age;
}
```

**SomeController**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("welcome", "Hello Thymeleaf.");
        model.addAttribute("student", new Student("张珊", 18));
        return "index";
    }
}
```

**index.html**

```xml
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:text="${welcome}">这里将要显示数据，但这些文字不显示。</p>
    <hr/>
    <p th:text="${student}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.name}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.age}">这里将要显示数据，但这些文字不显示。</p>
</body>
</html>
```

**选择表达式 *{}**

```xml
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:text="${welcome}">这里将要显示数据，但这些文字不显示。</p>
    <hr/>
    <p th:text="${student}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.name}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.age}">这里将要显示数据，但这些文字不显示。</p>
    <hr/>
    <div th:object="${student}">
        <p th:text="*{name}">这里将要显示数据，但这些文字不显示。</p>
        <p th:text="*{age}">这里将要显示数据，但这些文字不显示。</p>
    </div>
</body>
</html>
```

**URL 表达式 @{}**

在 01-primary 工程中添加服务：

```java
@RestController
public class SomeController {

    @GetMapping("/some")
    public String someHandler() {
        return "Hello";
    }

    @GetMapping("/find/{name}")
    public String findHandler(@PathVariable("name") String name) {
        return "查询指定的学生：" + name;
    }
}
```

index.html：

```xml
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:text="${welcome}">这里将要显示数据，但这些文字不显示。</p>
    <hr/>
    <p th:text="${student}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.name}">这里将要显示数据，但这些文字不显示。</p>
    <p th:text="${student.age}">这里将要显示数据，但这些文字不显示。</p>
    <hr/>
    <div th:object="${student}">
        <p th:text="*{name}">这里将要显示数据，但这些文字不显示。</p>
        <p th:text="*{age}">这里将要显示数据，但这些文字不显示。</p>
    </div>
    <hr/>
    <a th:href="@{'http://localhost:8888/find/' + ${student.name}}">查询1</a>
    <a th:href="@{|http://localhost:8888/find/${student.name}|}">查询2</a>
    <a th:href="@{|/xxx/find/${student.name}|}">查询3</a>
    <a th:href="@{|find/${student.name}|}">查询4</a>
</body>
</html>
```

### 17.3 逻辑运算属性

**th:if**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("gender", "male");
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:if="${gender} == 'male'">
        男
    </p>
    <p th:if="${gender} != 'male'">
        女
    </p>
</body>
</html>
```

**th:switch & th:case**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("student", new Student("张珊", 18));
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div th:switch="${student.age / 10}">
        <p th:case="0">儿童</p>
        <p th:case="1">少年</p>
        <p th:case="2">青年</p>
        <p th:case="3">中年</p>
        <p th:case="4">中年</p>
        <p th:case="5">中年</p>
        <p th:case="*">老年</p>
    </div>
</body>
</html>
```

**th:each**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("李思", 23));
        students.add(new Student("王杰", 18));
        students.add(new Student("张笃", 23));
        model.addAttribute("students", students);
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:each="stu, status: ${students}">
        <!--显示当前遍历对象是第几个，即序号（从1开始计数）-->
        <span th:text="${status.count}"></span>
        <!--显示当前遍历对象是的索引（从0开始计数）-->
        <span th:text="${status.index}"></span>
        <!--显示当前遍历对象是否是第一个-->
        <span th:text="${status.first}"></span>
        <!--显示当前遍历对象是否是最后一个-->
        <span th:text="${status.last}"></span>
        <!--显示当前遍历对象的序号是否是偶数-->
        <span th:text="${status.even}"></span>
        <!--显示当前遍历对象的序号是否是奇数-->
        <span th:text="${status.odd}"></span>
        <span th:text="${stu.name}"></span>
        <span th:text="${stu.age}"></span>
    </p>
</body>
</html>
```

另一种显示状态的方式：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:each="stu : ${students}">
        <!--显示当前遍历对象是第几个，即序号（从1开始计数）-->
        <span th:text="${stuStat.count}"></span>
        <!--显示当前遍历对象是的索引（从0开始计数）-->
        <span th:text="${stuStat.index}"></span>
        <!--显示当前遍历对象是否是第一个-->
        <span th:text="${stuStat.first}"></span>
        <!--显示当前遍历对象是否是最后一个-->
        <span th:text="${stuStat.last}"></span>
        <!--显示当前遍历对象的序号是否是偶数-->
        <span th:text="${stuStat.even}"></span>
        <!--显示当前遍历对象的序号是否是奇数-->
        <span th:text="${stuStat.odd}"></span>
        <span th:text="${stu.name}"></span>
        <span th:text="${stu.age}"></span>
    </p>
</body>
</html>
```

遍历 map：

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        Map<String, Student> map = new HashMap<>();
        map.put("stu7", new Student("田琪", 28));
        map.put("stu8", new Student("刘霸", 19));
        map.put("stu9", new Student("赵玖", 32));
        model.addAttribute("map", map);
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:each="entry : ${map}">
        <span th:text="${entryStat.count}"></span>
        <span th:text="${entry.key}"></span>
        <span th:text="${entry.value}"></span>
        <span th:text="${entry.value.name}"></span>
        <span th:text="${entry.value.age}"></span>
    </p>
</body>
</html>
```

**th:inline**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:inline="text">
        他的姓名是：[[${student.name}]]
    </p>
    <p>
        她的姓名是：[[${student.name}]]
    </p>

</body>
</html>
```

th:inline的取值可以有四种：

​		text：标签体中需要嵌入动态内容，默认值

​		javascript：js中需要嵌入动态内容

​		css：css中需要嵌入动态内容

​		non：不解析内嵌动态内容

### 17.4 html 标签相关属性

**th:text & th:utext**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("wel", "<h2>Hello, Thymeleaf<h2/>");
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:text="${wel}"></p>
    <p th:utext="${wel}"></p>
</body>
</html>
```

**th:name & th:value**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("attrName", "score");
        model.addAttribute("attrValue", 99);
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <input type="text" name="age" value="0"/>
    <input type="text" th:name="${attrName}" th:value="${attrValue}">
</body>
</html>
```

**th:src**

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("photo", "yangmi2.jpg");
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <img src="/images/yangmi1.jpg"/>
    <img th:src="|/images/${photo}|"/>
</body>
</html>
```

### 17.5 css/js 相关属性

**th:id & th:name**

这两个属性可以获取标签的动态 id 与 name 属性，以便在 js 中使用。

**th:style**

该属性用于获取标签的 css 动态样式。

**th:onclick**

该属性用于获取标签的单击事件所触发的动态事件，即单击事件所触发的方法名。这些 js 事件属性很多，都是以 th:on 开头。

### 17.6 内联属性与万能属性

**th:inline="text"**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <p th:inline="text">
        他的姓名是：[[${student.name}]]
    </p>
    <p>
        她的姓名是：[[${student.name}]]
    </p>
</script>
</body>
</html>
```

**th:inline="javascript"**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <script th:inline="javascript" type="text/javascript">
    alert([[${student.name}]]);
</script>
</body>
</html>
```

**th:inline="css"**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div id="reddiv">
        我的背景颜色为红色
    </div>
    <style>
        #[[${elementId}]] {
            width: 100px;
            height: 100px;
            background: [[${bgColor}]];
        }
    </style>
</script>
</body>
</html>
```

### 17.7 运算基础

thymeleaf 包含四种字面常量：文本、数字、布尔值、null

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("isClose", false);
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div>
        我爱你：<span th:text="'中国'"></span>
    </div>
    <div>
        3.14 + 6 = <span th:text="3.14 + 6"></span>
        3.14 + 6 = <span th:text="${3.14 + 6}"></span>
    </div>
    <div>
        <span th:if="${isClose} == false">欢迎光临</span>
        <span th:if="${isClose == false}">欢迎光临</span>
    </div>
</script>
</body>
</html>
```

thymeleaf 中的布尔常量为：true、false，不区分大小写

**null**

若对象未定义，则其值为null

若对象定义了，但其值被指定为null，则值为null

若集合已经被定义，但长度为0，此时的集合不为null

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model) {
        model.addAttribute("school", null);
        List<String> cities = new ArrayList<>();
        model.addAttribute("cities", cities);
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div>
        school：<span th:if="${school} == null">对象值为null</span> <br/>
        cities：<span th:if="${cities} != null">集合不为空</span> <br/>
        country：<span th:if="${country} == null">对象未定义</span> <br/>
    </div>
</script>
</body>
</html>
```

### 17.8 内置对象

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model, HttpServletRequest request, HttpSession session) {
        request.setAttribute("req", "reqValue");
        session.setAttribute("ses", "sesValue");
        session.getServletContext().setAttribute("app", "appValue");
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div th:text="${#request.getAttribute('req')}"></div>
    <div th:text="${#session.getAttribute('ses')}"></div>
    <div th:text="${#servletContext.getAttribute('app')}"></div>
    <div th:text="${#request.getContextPath()}"></div>
    <div th:text="${#request.getParameter('name')}"></div>
</body>
</html>
```

### 17.9 表达式实用对象

```java
@Controller
public class SomeController {

    @RequestMapping("index")
    public String indexHandle(Model model, HttpServletRequest request, HttpSession session) {
        int[] nums = {1, 2, 3, 4, 5};
        model.addAttribute("nums", nums);
        model.addAttribute("date", new Date());
        model.addAttribute("cardId","500234199906118098");
        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div th:text="${#aggregates.sum(nums)}"></div>
    <div th:text="${date}"></div>
    <div th:text="${#dates.format(date,'yyyy-MM-dd')}"></div>
    <div th:text="${#strings.substring(cardId, 6, 14)}"></div>
</body>
</html>
```

### 17.10 在项目中使用

复制 S11-ssdm 工程：

**pom.xml**

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
    <artifactId>S16-thymeleaf-ssdm</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
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

**application.yml**

```yaml
server:
  port: 8888
  servlet:
    context-path: /xxx
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
```

**index.html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <form th:action="|${#request.getContextPath()}/some/register|" method="post">
        姓名：<input type="text" name="name"> <br/>
        年龄：<input type="text" name="age"> <br/>
        <input type="submit" value="注册">
    </form>
    <hr>
    <form th:action="|${#request.getContextPath()}/some/find|" method="post">
        年龄上限：<input type="text" name="age"> <br>
        <input type="submit" value="查询">
    </form>
    <hr>
    <a th:href="|${#request.getContextPath()}/some/count|">查询学生总人数</a>
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

    @GetMapping("/index")
    public String indexHandler() {
        return "/index";
    }

    @PostMapping("/register")
    public String registerHandler(Student student, Model model) {
        model.addAttribute("student", student);
        studentService.addStudent(student);
        return "/html/welcome";
    }

    @PostMapping("/find")
    @ResponseBody
    public String findHandler(Integer age, Model model) {
        List<Student> students = studentService.findStudentBelowAge(age);
        model.addAttribute("students", students);
        return "/html/welcome";
    }

    @GetMapping("/count")
    @ResponseBody
    public Integer countHandler() {
        return studentService.countStudent();
    }
}
```

**welcome.html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Welcome</title>
</head>
<body>
    student = <span th:text="${student}"></span>
</body>
</html>
```

访问测试：http://localhost:8888/xxx/some/index

## 8. JSP

在 main 目录下新建 webapp 文件夹，并将其指定为 Web Resource Directories：

File --> Project Structure... --> Modules --> S08-jsp --> Web --> Web Resource Directories --> 添加目录

新增 index.jsp：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="" method="post">
        姓名：<input type="text" name="name"> <br/>
        年龄：<input type="text" name="age"> <br/>
        <input type="submit" value="注册">
    </form>
</body>
</html>
```

在浏览器中进行访问：http://localhost:8888/index.jsp 此时无法访问

**在 pom 文件中指定资源目录：**

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
    </resources>
</build>
```

**添加依赖：**

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
```

进行访问测试：http://localhost:8888/index.jsp 



**更详细的测试：**

**index.jsp：**

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
</body>
</html>
```

**实体类Student：**

```java
@Data
public class Student {
    private String name;
    private int age;
}
```

**SomeController：**

```java
@Controller
@RequestMapping("/some")
public class SomeController {

    @PostMapping("/register")
    public String registerHandler(Student student, Model model) {
        model.addAttribute("student", student);
        return "/jsp/welcome.jsp";
    }
}
```

**welcome.jsp：**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Welcome</title>
</head>
<body>
    student = ${student}
</body>
</html>
```

进行访问测试：http://localhost:8888/index.jsp

**进行前缀和后缀配置：**

```yaml
spring:
 mvc:
  view:
   prefix: /
   suffix: .jsp
```

**修改 Controller：**

```java
@Controller
@RequestMapping("/some")
public class SomeController {

    @PostMapping("/register")
    public String registerHandler(Student student, Model model) {
        model.addAttribute("student", student);
        return "jsp/welcome";
    }
}
```


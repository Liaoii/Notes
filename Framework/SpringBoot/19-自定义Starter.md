## 19. 自定义Starter

新建 wrap-spring-boot-starter 的SpringBoot 工程

导入依赖：

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
    <artifactId>wrap-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

**SomeService**

```java
@AllArgsConstructor
public class SomeService {
    private String before;
    private String after;

    public String wrap(String word) {
        return before + word + after;
    }
}
```

**SomeServiceProperties**

```java
@Data
@ConfigurationProperties("some.service")
public class SomeServiceProperties {
    private String prefix;
    private String suffix;
}
```

**SomeServiceAutoConfiguration**

```java
@Configuration
@ConditionalOnClass(SomeService.class)
@EnableConfigurationProperties(SomeServiceProperties.class)
public class SomeServiceAutoConfiguration {

    @Autowired
    private SomeServiceProperties properties;

    @Bean
    @ConditionalOnMissingBean
    public SomeService someService() {
        return new SomeService(properties.getPrefix(), properties.getSuffix());
    }

}
```



**使用自定义 Starter：**

引入依赖：

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
    <artifactId>S18-wraptest</artifactId>
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
            <groupId>com.liaoii.springboot</groupId>
            <artifactId>wrap-spring-boot-starter</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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

**SomeController**

```java
@RestController
public class SomeController {

    @Autowired
    private SomeService someService;

    @GetMapping("/some/{param}")
    public String someHandler(@PathVariable("param") String param) {
        return someService.wrap(param);
    }
}
```

访问测试：http://localhost:8080/some/China



**使用 enable 开关：**

```java
@Configuration
@ConditionalOnClass(SomeService.class)
@EnableConfigurationProperties(SomeServiceProperties.class)
public class SomeServiceAutoConfiguration {

    @Autowired
    private SomeServiceProperties properties;

    @Bean
    @ConditionalOnProperty(name = "some.service.enable", havingValue = "true", matchIfMissing = true)
    public SomeService someService1() {
        return new SomeService(properties.getPrefix(), properties.getSuffix());
    }

    @Bean
    @ConditionalOnMissingBean
    public SomeService someService2() {
        return new SomeService("", "");
    }
}
```

**application.yml**

```yaml
some:
  service:
    enable: false
    prefix: '&&&'
    suffix: '^^^'
```
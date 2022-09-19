## 2. Actuator

添加 Actuator 依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

启动工程并进行访问测试：http://localhost:8888/actuator/health，此时默认只开启了 health 监控终端

**修改 Actuator 监控终端使用独立的端口和上下文路径：**

```yaml
management:
  server:
    port: 9999
```

测试：http://localhost:9999/actuator/health

**修改 Actuator 的基础路径名称：**

```yaml
management:
  server:
    port: 9999
  endpoints:
    web:
      base-path: /act
```

测试：http://localhost:9999/act/health

**开启全部监控终端：**

```yaml
management:
  server:
    port: 9999
  endpoints:
    web:
      exposure:
        include: '*' #必须加单引号
```

访问 http://localhost:9999/actuator，结果如下：

```json
{"_links":
	{
		"self":{"href":"http://localhost:9999/actuator","templated":false},
		"beans":{"href":"http://localhost:9999/actuator/beans","templated":false},
		"caches-cache":{"href":"http://localhost:9999/actuator/caches/{cache}","templated":true},
		"caches":{"href":"http://localhost:9999/actuator/caches","templated":false},
		"health":{"href":"http://localhost:9999/actuator/health","templated":false},
		"health-path":{"href":"http://localhost:9999/actuator/health/{*path}","templated":true},
		"info":{"href":"http://localhost:9999/actuator/info","templated":false},
		"conditions":{"href":"http://localhost:9999/actuator/conditions","templated":false},
		"configprops":{"href":"http://localhost:9999/actuator/configprops","templated":false},
		"configprops-prefix":{"href":"http://localhost:9999/actuator/configprops/{prefix}","templated":true},
		"env":{"href":"http://localhost:9999/actuator/env","templated":false},
		"env-toMatch":{"href":"http://localhost:9999/actuator/env/{toMatch}","templated":true},
		"loggers":{"href":"http://localhost:9999/actuator/loggers","templated":false},
		"loggers-name":{"href":"http://localhost:9999/actuator/loggers/{name}","templated":true},
		"heapdump":{"href":"http://localhost:9999/actuator/heapdump","templated":false},
		"threaddump":{"href":"http://localhost:9999/actuator/threaddump","templated":false},
		"metrics-requiredMetricName":{"href":"http://localhost:9999/actuator/metrics/{requiredMetricName}","templated":true},
		"metrics":{"href":"http://localhost:9999/actuator/metrics","templated":false},
		"scheduledtasks":{"href":"http://localhost:9999/actuator/scheduledtasks","templated":false},
		"mappings":{"href":"http://localhost:9999/actuator/mappings","templated":false}
	}
}
```

**关闭部分监控终端：**

```yaml
management:
  server:
    port: 9999
  endpoints:
    web:
      exposure:
        include: '*'
        exclude: ["env","health"]
```

或者：

```yaml
management:
  server:
    port: 9999
  endpoints:
    web:
      exposure:
        include: '*'
        exclude: 
          - env
          - health
```

**配置 info 信息：**SpringBoot 2.7.1版本无法使用这种方式配置（?）

```yaml
info:
  company:
    name: Chongqing
  author:
    name: Liaoxi
  project:
    groupId: @project.groupId@
    artifactId: @project.artifactId@
```


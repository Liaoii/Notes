## 15. Servlet

**Servlet3.0 版本**

新建Servlet：

```java
@WebServlet("/some")
public class SomeServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().println("Hello Spring Boot Servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

在启动类上进行扫描：

```java
@ServletComponentScan("com.liaoii.springboot.servlet")
@SpringBootApplication
public class S14ServletApplication {

    public static void main(String[] args) {
        SpringApplication.run(S14ServletApplication.class, args);
    }

}
```

**Servlet2.5 +**

定义Servlet：

```java
public class SomeServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().println("Hello Spring Boot Servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

注册Servlet：

```java
@SpringBootApplication
public class S14Servlet2Application {

    public static void main(String[] args) {
        SpringApplication.run(S14Servlet2Application.class, args);
    }

    @Bean
    public ServletRegistrationBean<SomeServlet> getServletBean() {
        ServletRegistrationBean bean = new ServletRegistrationBean(new SomeServlet(), "/some");
        return bean;
    }
}
```

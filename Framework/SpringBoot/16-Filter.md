## 16. Filter

**Servlet 3.0 +**

定义 Filter：

```java
@WebFilter("/*")
public class SomeFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("信息被过滤");
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

在启动类上进行扫描：

```java
@ServletComponentScan("com.liaoii.springboot.filter")
@SpringBootApplication
public class S15FilterApplication {

    public static void main(String[] args) {
        SpringApplication.run(S15FilterApplication.class, args);
    }

}
```

@ServletComponentScan 不加路径也能进行扫描

**Servlet 2.5 +**

定义 Filter：

```java
public class SomeFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("信息被过滤");
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

注册并配置 Filter：

```
@SpringBootApplication
public class S15Filter2Application {

    public static void main(String[] args) {
        SpringApplication.run(S15Filter2Application.class, args);
    }

    @Bean
    public FilterRegistrationBean<SomeFilter> getFilterRegistrationBean() {
        SomeFilter someFilter = new SomeFilter();
        FilterRegistrationBean<SomeFilter> filterRegistrationBean = new FilterRegistrationBean<>(someFilter);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }

}
```
# Spring Security + JWT 实现用户认证与授权

## 1. 什么是 Spring Security

Spring Security 是 Spring 家族中的一个子项目，它提供了一套完整的安全框架，可以帮助我们快速的实现安全相关的功能，比如用户认证、授权、加密、防止
CSRF 攻击等。

## 2. 什么是 JWT

JWT 是 JSON Web Token 的缩写，它是一种基于 JSON 的开放标准（RFC 7519），用于在各方之间安全地传递信息。JWT
通常由三部分组成，分别是头部（header）、载荷（payload）、签名（signature）。

### 头部(header)

通常由两部分组成，令牌的类型（type）和使用的散列算法（algorithm），例如：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- 令牌的类型（type）通常是 JWT，或者是 JWS（JSON Web Signature），JWE（JSON Web Encryption）等。

- 令牌的算法（algorithm）通常是 HMAC SHA256 或者是 RSA等等。

- 令牌的类型和算法都是可以自定义的。

### 载荷(payload)

通常包含三个部分，标准中注册的声明（registered claims）、公共的声明（public claims）、私有的声明（private
claims），例如：

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

- 标准中注册的声明（registered claims）：这些是 JWT 规范中定义的一些字段，比如 iss（issuer，发布者）、exp（expiration
  time，过期时间）、sub（subject，主题）等。
  ```json
  {
      "iss": "joe",
      "exp": 1300819380,
      "http://example.com/is_root": true
  }
  ```
- 公共的声明（public claims）：这些是可以自定义的字段，但是为了避免冲突，最好加上一个命名空间，比如：app_user_id、app_user_name。
    ```json
    {
        "app_user_id": "1234567890",
        "app_user_name": "John Doe",
        "admin": true
    }
    ```
- 私有的声明（private claims）：与公共的声明的区别在于，这些是提供者和消费者私下约定的字段，不建议在标准中注册。

- 除了标准中注册的声明外，其他的声明都是可以自定义的。 通常情况下，我们会将用户的唯一标识（id）放在载荷中，以便在验证令牌的时候，可以从令牌中获取到用户的唯一标识。

  除了用户的唯一标识之外，还可以将用户的其他信息放在载荷中，以便在验证令牌的时候，可以从令牌中获取到用户的其他信息。
  例如，我们可以将用户的角色信息放在载荷中，以便在验证令牌的时候，可以从令牌中获取到用户的角色信息。

### 签名(signature)

是将前两个部分的json拼接中间加一点，再将这个拼接后的字符串用alg中的算法处理

```text
HMACSHA256(
base64UrlEncode(header) + "." +
base64UrlEncode(payload),
secret)
```

## 3. 代码实现

### 3.1 依赖

```xml

<dependencies>
    <!-- Spring Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <!-- JWT -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>${jjwt.version}</version>
    </dependency>
</dependencies>
```

### 3.2 自定义 UserDetailsService

```java

@Slf4j
@Service
public class JwtUserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserService userService;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userService.findByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException("no user found with username:{}" + username);
        } else {
            return JwtUserFactory.create(user);
        }
    }
}
```

### 3.3 自定义 UserDetails

### 3.4 自定义 JwtTokenUtil

### 3.5 自定义 JwtAuthenticationTokenFilter

### 3.6 自定义 JwtAuthenticationEntryPoint

### 3.7 配置 Spring Security

```java

@Configuration
@EnableWebSecurity // 开启 Spring Security
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Autowired
    private JwtAuthenticationEntryPoint unauthorizedHandler;

    @Bean
    public JwtAuthenticationTokenFilter authenticationTokenFilterBean() throws Exception {
        return new JwtAuthenticationTokenFilter();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(this.userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        httpSecurity
                // 由于使用的是JWT，我们这里不需要csrf
                .csrf().disable()

                // 基于token，所以不需要session
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).and()

                .authorizeRequests()
                // 对于获取token的rest api要允许匿名访问
                .antMatchers("/auth/**").permitAll()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();

        // 禁用缓存
        httpSecurity.headers().cacheControl();

        // 添加JWT filter
        httpSecurity.addFilterBefore(authenticationTokenFilterBean(), UsernamePasswordAuthenticationFilter.class);

        // 添加自定义未授权和未登录结果返回
        httpSecurity.exceptionHandling().authenticationEntryPoint(unauthorizedHandler);
    }
}
```

### 3.8 权限控制

```java

import cn.caoh2.app.service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * hasRole('ADMIN')：是否具有指定角色
     */
    @GetMapping("/user/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    /**
     * hasAnyRole('ADMIN','USER')：是否具有指定角色中的任意一个
     */
    @GetMapping("/user/{id}")
    @PreAuthorize("hasAnyRole('ADMIN','USER')")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    /**
     * hasAuthority('ROLE_ADMIN')：是否具有指定权限
     */
    @GetMapping("/user/{id}")
    @PreAuthorize("hasAuthority('ROLE_ADMIN')")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    /**
     * hasAnyAuthority('ROLE_ADMIN','ROLE_USER')：是否具有指定权限中的任意一个
     */
    @GetMapping("/user/{id}")
    @PreAuthorize("hasAnyAuthority('ROLE_ADMIN','ROLE_USER')")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    /**
     * hasIpAddress('ip地址')：是否来自指定ip地址
     */
    @GetMapping("/user/{id}")
    @PreAuthorize("hasIpAddress('127.0.0.1')")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

### 3.9 自定义权限注解

```java

import org.springframework.security.access.prepost.PreAuthorize;

import java.lang.annotation.*;

/**
 * 自定义权限注解
 */
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@PreAuthorize("hasRole('ADMIN')")
public @interface AdminRole {
}
```

### 3.10 自定义权限控制

## 4. CSRF

### 4.1 什么是 CSRF

CSRF（Cross-site request
forgery）跨站请求伪造，是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。攻击者盗用了你的身份，以你的名义发送恶意请求，对服务器来说，这个请求是完全合法的，但却完成了攻击者所期望的一个操作，比如以你的名义发送邮件、发消息，盗取你的账号，甚至于购买商品、虚拟货币转账等，造成不可挽回的损失。

### 4.2 CSRF 攻击原理

- 用户登录网站 A，并在本地生成 Cookie
- 用户在没有退出网站 A 的情况下，访问了危险网站 B
- 网站 B 包含了网站 A 的一个请求地址，并且会自动执行这个请求
- 用户的 Cookie 会被一起发送到网站 A，网站 A 无法区分这个请求是用户自己发起的还是网站 B 发起的，于是就执行了网站 B 的请求
- 网站 A 执行了网站 B 的请求，造成了 CSRF 攻击
- CSRF 攻击的危害取决于网站 A 的业务逻辑，比如转账、发消息、购买商品等

### 4.3 防御 CSRF

- 验证 HTTP Referer 字段
- 在请求地址中添加 token，并验证该 token
- 在请求头中自定义参数，并验证该参数
- 在 Cookie 中设置 CSRF token，并验证该 token

### 4.4 Spring Security 防御 CSRF

原理：Spring Security 会在用户登录成功后，生成一个 CSRF token，并将该 token 保存在 Session 中，
同时在响应头中返回一个名为 X-CSRF-TOKEN 的参数，该参数的值就是 CSRF token。当用户访问需要验证 CSRF token 的接口时，
需要在请求头中带上该 token，Spring Security 会从请求头中获取该 token，并与 Session 中的 token 进行比对，如果一致，则通过验证，否则拒绝访问。

## 认证成功处理器

在UsernamePasswordAuthenticationFilter过滤器中，如果认证成功，会调用成功处理器AuthenticationSuccessHandler;
自定义成功处理器，实现AuthenticationSuccessHandler接口，重写onAuthenticationSuccess方法。

```java
import org.springframework.stereotype.Component;

@Component
public class MyAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write("登录成功");
        out.flush();
        out.close();
    }
}
```

在WebSecurityConfig中配置

```java
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private MyAuthenticationSuccessHandler myAuthenticationSuccessHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        () -> http.formLogin()
                .successHandler(myAuthenticationSuccessHandler) // 登录成功处理器
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated();
    }
}
```

## 认证失败处理器

在UsernamePasswordAuthenticationFilter过滤器中，如果认证失败，会调用失败处理器AuthenticationFailureHandler;
自定义失败处理器，实现AuthenticationFailureHandler接口，重写onAuthenticationFailure方法。

```java
import org.springframework.stereotype.Component;

@Component
public class MyAuthenticationFailureHandler implements AuthenticationFailureHandler {
    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write("登录失败");
        out.flush();
        out.close();
    }
}
```

在WebSecurityConfig中配置

```java
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private MyAuthenticationFailureHandler myAuthenticationFailureHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        () -> http.formLogin()
                .failureHandler(myAuthenticationFailureHandler) // 登录失败处理器
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated();
    }
}
```

## 退出成功处理器

在LogoutFilter过滤器中，如果退出成功，会调用成功处理器LogoutSuccessHandler;
自定义成功处理器，实现LogoutSuccessHandler接口，重写onLogoutSuccess方法。

```java
import org.springframework.stereotype.Component;

@Component
public class MyLogoutSuccessHandler implements LogoutSuccessHandler {
    @Override
    public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write("退出成功");
        out.flush();
        out.close();
    }
}
```

在WebSecurityConfig中配置

```java
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private MyLogoutSuccessHandler myLogoutSuccessHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        () -> http.logout()
                .logoutSuccessHandler(myLogoutSuccessHandler) // 退出成功处理器
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated();
    }
}
```

## 5. 其他认证方式

### 5.1 短信验证码登录

> 本文使用的是阿里云短信服务，需要先注册阿里云账号，然后在控制台创建短信服务，创建完毕后，会生成 AccessKey ID 和 AccessKey
> Secret，需要将这两个值填入 application.yml 中。

#### 5.1.1 配置短信验证码登录

### 5.2 社交登录

#### 5.2.1 配置社交登录

#### 5.2.2 配置社交登录的回调地址

#### 5.2.3 配置社交登录的用户信息获取

#### 5.2.4 配置社交登录的绑定和解绑

#### 5.2.5 配置社交登录的用户注册

#### 5.2.6 配置社交登录的用户信息修改

#### 5.2.7 配置社交登录的用户信息获取

## 6. Spring Security OAuth2

### 6.1 OAuth2 简介

OAuth2 是一个授权框架，它可以让第三方应用获取到用户的授权，而不需要获取用户的用户名和密码。

### 6.2 OAuth2 授权流程

1. 客户端 (Client) 向授权服务器 (Authorization Server) 发送授权请求 (Authorization Request)。
2. 授权服务器验证客户端的身份和权限，并要求用户授权。
3. 用户授权后，授权服务器向客户端颁发授权码 (Authorization Grant)。
4. 客户端使用授权码向授权服务器请求访问令牌 (Access Token)。
5. 授权服务器验证授权码的有效性，并颁发访问令牌。
6. 客户端使用访问令牌向资源服务器 (Resource Server) 发送受保护的资源请求。
7. 资源服务器验证访问令牌的有效性，并向客户端返回所请求的受保护资源。


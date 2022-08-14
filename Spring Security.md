# Spring Security

## Spring Security原理

本质上是一个过滤器链



## web权限方案

> 用户认证
>
> 用户授权

![1658758660330](Spring%20Security.assets/1658758660330.png)

![1658758681643](Spring%20Security.assets/1658758681643.png)



```java
@Configuration
//WebSecurityConfigurerAdapter 让 Spring Security 可以自动的扫描你的应用程序，并且自动的配置 Spring Security。
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //加密密码
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        String password = bCryptPasswordEncoder.encode("123456");
        auth.inMemoryAuthentication()
                .withUser("user").password(password).roles("USER")
                .and()
                .withUser("admin").password(password).roles("ADMIN");
    }

    @Bean //将 PasswordEncoder 对象注入到 Spring 容器中。
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(); 
    }
}
```

自定义编写

```java
@Configuration
public class SecurityConfigV2 extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Bean
        //将 PasswordEncoder 对象注入到 Spring 容器中。
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

```java
@Service("userDetailService")
public class MyUserDetailService implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username) {
        // 模拟数据库查询
        // 可以从数据库中查询用户信息，然后返回 UserDetails 对象
        //authorities: 权限列表 不能为空
        List<GrantedAuthority> authorities = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_USER");
        return new User("caoh2", new BCryptPasswordEncoder().encode("123456"), authorities);
    }


}
```

### 认证授权注解

### 1. @Secured

用户具备某种角色才可以访问该方法
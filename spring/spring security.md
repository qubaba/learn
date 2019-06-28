# 浏览器

### 第一步

继承 ```WebSecurityConfigurerAdapter``` 类并重写```configure(HttpSecurity http)```方法,

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
            .and()
            .authorizeRequests() // 下面的配置是认证的配置
            .anyRequest() //任何请求
            .authenticated() ; // 都需要身份认证
    }
```

### 第二步

service继承`` UserDetailsService``类，重写```loadUserByUserName(String userName)```方法，返回的是userDetails或者实现类。

```java
@Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
      	//自己的业务逻辑
        return new User();
    }
```

#### UserDetails属性

``isAccountNonExpired``：没有过期，true为没有过期。

``isAccountNonLocked``:没有锁定，true为没有锁定。

``isEnabled``:可用，true为可用。

``isCredentialsNonExpired``:密码没有过期，true为没有过期。


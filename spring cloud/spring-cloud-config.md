[TOC]





# 1. 普通配置中心

## config server

###  POM文件

```mave
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

### git版配置文件

```properties
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/qubaba/spring-cloud-config  # git路径
          username:  # 私有仓库的话需要输入git的账号密码
          password: 
          search-paths: /spring-cloud-config # 查找该路径下的所有文件
          default-label: spring-cloud-config # 默认的分支
```

### 启动类

```
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}


```

关键注解为：`@EnableConfigServer`表示启动类为配置服务类。



**访问到配置服务器的配置文件后会在本地生成一份配置文件的副本。**

## config Client

###  POM文件

```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

### 配置文件

spring cloud config 的配置文件需要提前加载所以需要在`resources`目录下新建文件`bootstrap.yml`文件

```properties
spring:
  profiles:
    active: dev # 默认加载配置文件
  application:
    name: config
  cloud:
    config:
      uri: http://localhost:9000  # 配置服务器地址
      name: ${spring.application.name} # application Name
      profile: ${spring.profiles.active} # 那个类型的配置为文件
```

# 2. 使用Bus的动态配置中心

## server端配置

### POM文件

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

### 配置文件

**application.yml**

```properties
server:
  port: 9000

spring:
  application:
    name: config-server-bus
  cloud:
    config:
      server:
        git:
          username: git userName
          password: gitPassword
          uri:git Url
          search-paths: /spring-cloud-config
          default-label: spring-cloud-config
    bus:
      trace:
        enabled: true # 启用日志跟踪
  rabbitmq:   # rabbitmq的配置信息
    host: localhost
    port: 5672
    username: guest
    password: guest

management:   # 管理端信息
  endpoints:
    web:
      exposure:
        include: bus-env,bus-refresh  # 暴露端点
  endpoint:
    health:
      show-details: always  # 日志显示时机
```

## client端配置

### POM文件

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-config</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

### 配置文件

**bootstrap.yml**

```properties
server:
  port: 8082

spring:
  rabbitmq: # Rabbit Mq 配置信息
    host: localhost
    port: 5672
    username: guest
    password: guest
  profiles:
    active: dev # 启动项目加载的配置文件类型
  application:
    name: config
  cloud:
    config:
      uri: http://localhost:9000  # 配置服务器地址
      name: ${spring.application.name} # 服务名称
      profile: ${spring.profiles.active} # 那个类型的配置为文件  文件类型分为 dev test prod
    bus:
      trace:
        enabled: true
management:
  endpoints:
    web:
      exposure:
        include: bus-env,bus-refresh
  endpoint:
    health:
      show-details: always
    refresh:
      enabled: true

```

### 原来代码需要修改的

需要在原来的组件类上加该注解`@RefreshScope`表示这个类是刷新作用域，其他注解保持不变。

组件类定义：使用了类级注解`@RestController`、`@Controller`、`@Service`、`@Repository`、`@Component`、`@Configuration`的类



**注意：服务端和客户端都需要依赖amqp的依赖。**

# 3. 使用mysql作为配置文件的地址

## server端配置

### POM文件

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.31</version>
</dependency>
```

### 配置文件

properties.yml

```properties
server:
  port: 9000

spring:
  application:
    name: config-server-jdbc
  cloud:
    config:
      profile: jdbc   # 使用jdbc作为配置文件
      label: master
      server:
        jdbc:
          sql: SELECT `key`,`value` from config_properties where application_name=? and profile=? and lable=?   # sql
    refresh:
      refreshable: none   # 使用Jdbc作为配置文件会造成循环依赖，使用此注解可解决问题
  profiles:
    active: jdbc  #默认文件类型 jdbc

  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/spring_cloud
#  main:
#    allow-bean-definition-overriding: true 使用Jdbc作为配置文件会造成循环依赖，使用此注解可解决问题

```

## 客户端保持不变

# 4. 动态刷新配置中心

## server端保持不变

### 新增项目 config-auto-refresh

### POM文件

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### 配置类

新增配置类`AutoConfigRefresh`

```java
package com.spring.cloud.configautorefreshclient.config;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.cloud.autoconfigure.RefreshAutoConfiguration;
import org.springframework.cloud.endpoint.RefreshEndpoint;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.ScheduledAnnotationBeanPostProcessor;
import org.springframework.scheduling.annotation.SchedulingConfigurer;
import org.springframework.scheduling.config.IntervalTask;
import org.springframework.scheduling.config.ScheduledTaskRegistrar;

/**
 * @Classname AutoConfigRefresh
 * @Description TODO
 * @Date 2019/2/25 21:46
 * @Created by qp
 */
@Configuration
@ConditionalOnClass(RefreshEndpoint.class) // 具有这个bean的时候生效,如果没有该Bean此配置类不生效
@ConditionalOnProperty("spring.cloud.refreshInterVal") // 具有该属性时生效，如果没有该属性此配置类不生效
@AutoConfigureAfter(RefreshAutoConfiguration.class) // 在RefreshAutoConfiguration之前进行配置
public class AutoConfigRefresh implements SchedulingConfigurer {

    private static final transient Logger log = LoggerFactory.getLogger(AutoConfigRefresh.class);

    @Value("${spring.cloud.refreshInterVal}")
    private int refreshInterVal; // 定义自动刷新时间

    @Autowired
    private RefreshEndpoint refreshEndpoint; // 注入刷新端点信息

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {

        log.info(String.format("-------------服务已经启动,刷新间隔时间 %s 秒----------------",refreshInterVal));
        final long interVal = getRefreshInterValInMilliseconds();

        // 注册延迟定时任务
        taskRegistrar.addFixedDelayTask(new IntervalTask(() -> refreshEndpoint.refresh(), interVal, interVal));
    }

    private long getRefreshInterValInMilliseconds() {
        return refreshInterVal * 1000;
    }

    @ConditionalOnMissingBean(ScheduledAnnotationBeanPostProcessor.class) // 找不到该Bean是运行下面的代码
    @EnableScheduling // 开启定时任务
    @Configuration
    public static class EnableSchedulingConfigProperties{

    }
}

```

### 新增spring.factories文件

在`resources`目录下新建`META-INF`目录，并新增`spring.factories`文件。

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.spring.cloud.configautorefreshclient.config.AutoConfigRefresh
```

在调用`EnableAutoConfiguration`注解时调用`AutoConfigRefresh`类。

**注意：上面注解用的是在具有某些条件时才生效，所以在引用配置类的配置文件没有需要属性时该配置类不生效。**

## client端

### POM文件

在pom文件中新增对`config-auto-refresh`的依赖。

### 配置文件

添加`spring.cloud.refreshInterVal`属性。
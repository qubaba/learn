# Spring Boot 核心技术

## 核心特性

### 组件自动装配

该特性可以分为两部分进行理解，即组件和自动装配。

**组件：**可以把Spring Boot官方提供的starter或第三方提供的starter理解为组件。第三方starter包括自己创建的starter。

**自动装配：**这个是Spring Boot的主要核心特性。通过`spring-boot-autoconfigure`中的`@EnableAutoConfiguration`注解激活使用。

**自动装配实现方式：**

1. 在项目的resources目录下新建META-INF目录并在该目录下新建`spring.factories`文件。该文件内容定义形式为key=value[,value]

2. 在`spring.factories`文件中增加`@EnableAutoConfiguration`注解调用时触发的方法。

   ```properties
   org.springframework.boot.autoconfigure.EnableAutoConfiguration=需要调用的configuration类
   ```

**涉及到的注解 **

1. `@Configuration`：声明一个Bean为配置Bean。
2. `@AutoConfigureAfter(Class<?>[] value)`：在指定的自动配置类之后执行。注解参数必须为已经自动配置的类。
3. `@ConditionalOnProperty`：只有在指定的配置文件存在时才触发自动配置。
4. `@ConditionalOnMissingBean`：只有在BeanFactory中不包含制定类或者指定名称的Bean是匹配。如果该注解应用在方法上，那么Bean默认为方法的返回值类型。

### 嵌入式web容器

​	Spring Boot集成了传统的Web容器如tomcat、jetty等，在Spring Boot 2.0 中新增了对WebFlux的支持。开发人员可以根据自己的需求使用相应的容器。由于`spring-boot-starter-web`默认的是tomcat容器，如果要使用其他容器，需要在pom.xml中引入相应容器依赖的同时在`spring-boot-starter-web`依赖中排除tomcat的依赖。

### 生产准备

1. 健康检查、指标：`spring-boot-starter-actuator`中提供了一系列的健康检查及系统指标，通过这些健康检查及系统指标可以结合一些组件比如：Spring Boot Admin对生产环境的健康状态进行监控。
2. 外部化配置：对于这个可以结合一些Spring Cloud的组件，如：Spring Cloud Config进行文件外部化配置。
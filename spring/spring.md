## Spring 加载资源文件

Spring加载资源文件目前了解三种，

1. `@PostConstruct`在Context加载完成之后加载。在创建各个Bean对象之前加载。
2. 实现`ApplicationRunner`的`run`方法，Bean加载完成之后加载，优先于`CommandLineRunner`执行。

```java
@Component
@Order(2)
public class ApplicationRunnerTest implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("我略迟于CommandLineRunner执行！！！");
    }
}
```

如果对于顺序没有要求，并且使用的是jdk1.8那么可以采用下面的写法

```java
@Bean
public ApplicationRunner applicationRunner(){
    return args -> System.out.println("我优先于CommandLineRunner执行！！！");
}
```



3. 实现`CommandLineRunner`的`run`方法，Bean加载完成之后加载，`ApplicationRunner`之后执行，可以通过`@Order(order)`调整加载顺序。

```java
@Component
@Order(1)
public class CommandLineRunnerTest implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("我优先于ApplicationRunner执行！！！");
    }
}
```

如果对于顺序没有要求，并且使用的是jdk1.8那么可以采用下面的写法

```java
@Bean
public CommandLineRunner commandLineRunner(){
    return args -> System.out.println("我略迟于ApplicationRunner执行！！！");
}
```




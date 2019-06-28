# AMQP定义

AMQP：高级消息队列（Advanced Message Queuing Protocol），一个提供统一消息服务的应用层标准高级消息队列协议，是应用层协议的一个开放标准,为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件同产品，不同的开发语言等条件的限制。



# AMQP架构

AMQ 作为中间层服务，把消息生产和消费分隔开来，当消息生产者出现异常，不影响消费者对消息的消费，当消费者异常时，生产者生产的消息可以存放到服务的内存或者磁盘，不会影响到消费的速率，同时，消息也可以基于路由的规则可以投递到指定的消费者消费。

AMQ 基于模块化通过 Exchange 和 Message Queue 两个组建组合实现消息路由分发：

1. Exchange：基于Message produce和routing Key可以将消息投递到指定的 Message Queue；

2. Message Queue：能够将发送过来的消息进行存储，同时将消息转发给Message consumer；

Exchange 和 Message Queue之间存在绑定关系，消息到了 Exchange 后基于routing Key可以将消息投递到已绑定且符合routing Key的 Message Queue。

## Exchange

AMQP 四种不同类型的Exchange

1. Fanout Exchange 不管消息的routing Key和消息的头/值是什么，消息都将路由到所有队列。
2. Direct Exchange 消息的routing Key 与binding的routing Key直接匹配的话，消息将会路由到该队列上。
3. Topic Exchange 消息的routing Key 与binding的routing Key符合通配符匹配的话，消息将会路由到该队列上。
4. Header Exchange 如果消息表中的头信息和值都与binding参数表中想匹配，消息将会路由到该队列上。



# Spring Boot 整合Rabbit Mq

1. POM依赖

   ```maven
   <dependency>
   	<groupId>org.springframework.boot</groupId>
   	<artifactId>spring-boot-starter-amqp</artifactId>
   </dependency>
   ```

2. properties

   ```properties
   spring.rabbitmq.host=localhost
   spring.rabbitmq.port=5672
   spring.rabbitmq.username=guest
   spring.rabbitmq.password=guest
   ```

3. 消息生产者

   ```java
   @Configuration
   public class Msg {
   
       // 交换机名称
       public static final String EXCHANGE_TOPICS_TEST = "exchange_topics_test";
   
       // 队列名称
       public static final String TOPIC_TEST = "topic_test";
   
       // 路由key
       public static final String ROUTING_KEY = "info.#";
   
       // 声明交换机
       @Bean(EXCHANGE_TOPICS_TEST)
       Exchange EXCHANGE_TOPICS_TEST() {
           return ExchangeBuilder.topicExchange(EXCHANGE_TOPICS_TEST).build();
       }
   
       // 声明队列
       @Bean(TOPIC_TEST)
       Queue TOPIC_TEST() {
           return new Queue(TOPIC_TEST);
       }
   
       @Bean
       Binding binding(@Qualifier(TOPIC_TEST) Queue queue, @Qualifier(EXCHANGE_TOPICS_TEST) Exchange exchange){
           return BindingBuilder.bind(queue).to(exchange).with(ROUTING_KEY).noargs();
       }
   }
   ```

4. 生产消息

   ```java
   	@Autowired
       RabbitTemplate rabbitTemplate;
   
       @Test
       public void contextLoads() {
   
           String message = "hello ----------- amqp";
           rabbitTemplate.convertAndSend(Msg.EXCHANGE_TOPICS_TEST, "info.test", message);
       }
   ```

5. 消息消费者

   ```java
   	@RabbitListener(queues = Msg.TOPIC_TEST)
       public void consumerMsg(String message){
   
           System.out.println(message);
       }
   ```

   

# 相关解释

1. Exchange 交换机
2. Message Queue 消息队列
3. Message produce 消息生产者
4. routing Key 路由规则
5. Message consumer 消息消费者
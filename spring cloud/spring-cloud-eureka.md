# eureka开启http basic认证

## eureka-server

导入`spring security`包，开启`http basic`认证。

```maven
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-security</artifactId>
</dependency>
```

```java

package com.example.eurekaserver.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
public class WebSecurityHandler extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.httpBasic().and()
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and().csrf().disable();

    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .passwordEncoder(new BCryptPasswordEncoder())
                .withUser("admin")
                .password(new BCryptPasswordEncoder().encode("admin")).roles("admin");
    }
}

```

## eureka-client

注册中心地址需要改为`http://username:password@host:port/uri`





# eureka 使用https认证

## 生成server数字证书

```shell
keytool -genkeypair -alias server -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore server.p12 -validity 3650
-----------------------------解释----------------------------
keytool -genkeypair -alias 别名 -storetype 存储类型 -keyalg 秘钥算法 -keysize 秘钥大小 -keystore 秘钥库名称 -validity 有效时间
```

## 生成client证书

```shell
keytool -genkeypair -alias client -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore client.p12 -validity 3650
-----------------------------解释----------------------------
keytool -genkeypair -alias 别名 -storetype 存储类型 -keyalg 秘钥算法 -keysize 秘钥大小 -keystore 秘钥库名称 -validity 有效时间
```

## 导出server cer证书

```shell
keytool -export -alias server -file server.crt -keystore server.p12
-----------------------------解释-----------------------------
keytool -export -alias 别名 -file 生成文件名 -keystore 秘钥库名称
```

## 导出client cer证书

```shell
keytool -export -alias client -file client.crt -keystore client.p12
-----------------------------解释-----------------------------
keytool -export -alias 别名 -file 生成文件名 -keystore 秘钥库名称
```

## 将server.cer导入到client.p12中使client端信任server证书

```shell
keytool -import -alias server -file server.crt -keystore client.p12
-----------------------------解释-----------------------------
keytool -import -alias 别名 -file 导出的server.crt证书 - keystore 客户端秘钥库文件
```

**注意：此处使用的.p12证书的密码**

## 将client.cer导入到server.p12中，使server端信任client证书

```shell
keytool -import -alias client -file client.crt -keystore server.p12
-----------------------------解释-----------------------------
keytool -import -alias 别名 -file client.crt证书 -keystore 服务端秘钥库文件
```

**注意：此处使用的是.p12证书的密码**

## 修改server

配置文件

```properties
server:
  port: 8761
  ssl:
    enabled: true # 是否开启ssl
    key-store: classpath:server.p12 # 数字证书的p12文件路径
    key-store-password: qupeng123 # 数字证书的密码
    key-store-type: PKCS12 # 数字证书存储类型
    key-alias: server   #数字证书别名
```

```properties
eureka:
  instance:
    hostname: localhost   # eureka实例地址
    secure-port: ${server.port}   # https端口号
    non-secure-port-enabled: false  # 关闭http端口
    secure-port-enabled: true # 开启https端口
    home-page-url: https://${eureka.instance.hostname}:${eureka.instance.secure-port}/ # 根目录访问路径
    status-page-url: https://${eureka.instance.hostname}:${eureka.instance.secure-port}/
  client:
    fetch-registry: false
    register-with-eureka: false
```

**注意：需要把server.p12文件放在resource文件夹下**

## 修改client

导入依赖

```java
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.5</version>
</dependency>
```

配置文件

```properties
ssl:
  key-store: client.p12 #自定义属性
  key-store-password: qupeng456 # 自定义属性
```

配置类

```java
@Configuration
public class EurekaHttpsClientConfig1 {

    @Value("${eureka.client.ssl.key-store}")
    String keyStoreFileName;
    @Value("${eureka.client.ssl.key-store-password}")
    String keyStorePassword;


    @Bean
    public DiscoveryClient.DiscoveryClientOptionalArgs discoveryClientOptionalArgs() throws CertificateException, NoSuchAlgorithmException, KeyStoreException, IOException, KeyManagementException {
        EurekaJerseyClientImpl.EurekaJerseyClientBuilder builder = new EurekaJerseyClientImpl.EurekaJerseyClientBuilder();
        builder.withClientName("eureka-https-client");
        URL resource = this.getClass().getClassLoader().getResource(keyStoreFileName);
        SSLContext sslContext = new SSLContextBuilder()
                .loadTrustMaterial(resource, keyStorePassword.toCharArray())
                .build();
        builder.withCustomSSL(sslContext);
        builder.withMaxTotalConnections(10);
        builder.withMaxConnectionsPerHost(10);
        DiscoveryClient.DiscoveryClientOptionalArgs args = new DiscoveryClient.DiscoveryClientOptionalArgs();
        args.setEurekaJerseyClient(builder.build());
        return args;

    }

}
```

**注意：需要把client.p12文件放在resource文件夹下**
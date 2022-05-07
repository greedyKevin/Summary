### 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>2021.0.1.0</version>
</dependency>

<!-- spring boot 版本过高 添加 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>


<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>2021.0.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2021.0.1.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



### 客户端配置

```yaml
server:
  port: 10086
spring:
  application:
    name: test
  cloud:
    nacos:
      # nacos 服务器
      server-addr: localhost:8848
      config:
      	# 文件后缀
        file-extension: yml
        # 空间名
        namespace: dev
        # 组别
        group: tan
  profiles:
    active: dev
```





配置对应： application.name-namespace.file-extension

**profiles.active与namespace与空间要相对应**

![image-nacos-image](https://raw.githubusercontent.com/greedyKevin/Summary/main/images/nacos-image.png)

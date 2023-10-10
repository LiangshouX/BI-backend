<p align="center">
<a>
    <img src="https://img.shields.io/badge/Spring Boot-2.7.2-brightgreen.svg" alt="Spring Boot">
    <img src="https://img.shields.io/badge/MySQL-8.0.20-orange.svg" alt="MySQL">
    <img src="https://img.shields.io/badge/Java-1.7.0__371-blue.svg" alt="Java">
    <img src="https://img.shields.io/badge/Redis-7.0.9-red.svg" alt="Redis">
    <img src="https://img.shields.io/badge/RabbitMQ-3.9.11-orange.svg" alt="RabbitMQ">
    <img src="https://img.shields.io/badge/MyBatis--Plus-3.5.2-blue.svg" alt="MyBatis-Plus">
    <img src="https://img.shields.io/badge/Redisson-3.21.3-yellow.svg" alt="Redisson">
    <img src="https://img.shields.io/badge/Gson-3.9.1-blue.svg" alt="Gson">
    <img src="https://img.shields.io/badge/MyBatis-2.2.2-yellow.svg" alt="MyBatis">
</a>
</p>

# AIGC智能数据分析平台

> By:	Liangshou		
>
> Conduct By:	[编程导航知识星球](https://yupi.icu) 
>
> Date:	2023.10



本项目仅开发了基础的平台后端。将在后续开发过程中进一步探索完善。

[toc]

## 项目介绍

### 1. 简介

基于 Spring Boot + MQ + AIGC 的智能数据分析平台。结合 AIGC，用户只需要导入原始数据、输入分析诉求，就能自动生成可视化图表及分析结论，实现数据分析降本增效。

> AIGC： AI生成内容

### 2. 技术选型

* Java Spring Boot（使用[编程导航知识星球](https://yupi.icu) 提供发热模版来初始化）
* MySQL数据库
* MyBatis-plus 及 MybatX自动生成
* Redis + Redisson限流
* RabbitMQ消息队列
* [鱼聪明](yucongming.com) AI SDK
* Easy Excel表格数据处理
* Swagger + Knife4j 接口文档生成

## 项目架构



### 基础架构

基础架构：客户端输入分析诉求和原始数据，向业务后端发送请求。业务后端利用AI服务处理客户端数据，保持到数据库，并生成图表。处理后的数据由业务后端发送给AI服务，AI服务生成结果并返回给后端，最终将结果返回给客户端展示。

![](https://user-images.githubusercontent.com/94662685/248857523-deff2de3-c370-4a9a-9628-723ace5ab4b3.png)

### 优化项目架构-异步化处理

优化流程（异步化）：客户端输入分析诉求和原始数据，向业务后端发送请求。业务后端将请求事件放入消息队列，并为客户端生成取餐号，让要生成图表的客户端去排队，消息队列根据I服务负载情况，定期检查进度，如果AI服务还能处理更多的图表生成请求，就向任务处理模块发送消息。

任务处理模块调用AI服务处理客户端数据，AI 服务异步生成结果返回给后端并保存到数据库，当后端的AI工服务生成完毕后，可以通过向前端发送通知的方式，或者通过业务后端监控数据库中图表生成服务的状态，来确定生成结果是否可用。若生成结果可用，前端即可获取并处理相应的数据，最终将结果返回给客户端展示。在此期间，用户可以去做自己的事情。
![image](https://user-images.githubusercontent.com/94662685/248858431-6dbf41e0-adfe-40cf-94da-f3db6c73b69d.png)



### 目录结构

```shell

+---.idea
+---sql		// SQL代码
+---src
|   +---main
|   |   +---java
|   |   |   \---com
|   |   |       \---liangshou
|   |   |           \---springbootinit
|   |   |               +---annotation	// 权限控制
|   |   |               +---aop
|   |   |               +---api
|   |   |               +---bizmq		// MQ配置
|   |   |               +---common		// 通用类
|   |   |               +---config		// 项目配置类
|   |   |               +---constant	// 项目常量类
|   |   |               +---controller	// 前端请求接口
|   |   |               +---esdao
|   |   |               +---exception	// 全局异常处理类
|   |   |               +---job
|   |   |               |   +---cycle
|   |   |               |   \---once
|   |   |               +---manager	// 项目管理类
|   |   |               +---mapper	// 数据访问层
|   |   |               +---model	// 项目实体类
|   |   |               |   +---dto
|   |   |               |   |   +---chart
|   |   |               |   |   +---file
|   |   |               |   |   +---post
|   |   |               |   |   +---postfavour
|   |   |               |   |   +---postthumb
|   |   |               |   |   \---user
|   |   |               |   +---entity
|   |   |               |   +---enums
|   |   |               |   \---vo
|   |   |               +---mq
|   |   |               +---service
|   |   |               |   \---impl
|   |   |               \---utils
|   |   \---resources
|   |       +---mapper
|   |       \---META-INF
|   \---test	//测试单元
|       \---java
|           \---com
|               +---liangshou
|                   \---springbootinit
|                       +---bizmq
|                       +---esdao
|                       +---manager
|                       +---mapper
|                       +---service
|                       \---utils
|               
\---target
```



## 快速启动 

1. 下载/拉取本项目到本地
2. 通过 IDEA 打开项目，等待依赖的下载
3. 修改配置文件 `application.yaml` 的信息，例如数据库、Redis、RabbitMQ配置等
4. 修改配置信息完成后，运行 `MainApplication` 程序启动项目

### MySQL 数据库

1）修改 `application.yml` 的数据库配置为你自己的：

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/my_db
    username: root
    password: 123456
```

2）执行 `sql/create_table.sql` 中的数据库语句，自动创建库表

3）启动项目，访问 `http://localhost:8080/api/doc.html` 即可打开接口文档，不需要写前端就能在线调试接口了

![](doc/swagger.png)

### Redis 分布式登录

1）修改 `application.yml` 的 Redis 配置为你自己的：

```yml
spring:
  redis:
    database: 1
    host: localhost
    port: 6379
    timeout: 5000
    password: 123456
```

2）修改 `application.yml` 中的 session 存储方式：

```yml
spring:
  session:
    store-type: redis
```

3）移除 `MainApplication` 类开头 `@SpringBootApplication` 注解内的 exclude 参数：

修改前：

```java
@SpringBootApplication(exclude = {RedisAutoConfiguration.class})
```

修改后：


```java
@SpringBootApplication
```


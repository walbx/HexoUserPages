---
title: 正确、安全地停止SpringBoot应用
date: 2016-06-27 16:35:29
categories: 我用
tags: [Spring Boot, 微服务]
description: 基于官方特性的两种正确、安全地停止Spring Boot应用服务的方法
---
## 引言
[Spring Boot](http://projects.spring.io/spring-boot/)，作为Spring框架对“约定优先于配置(Convention Over Configuration)”理念的最佳实践的产物，它能帮助我们很快捷的创建出独立运行、产品级别的基于Spring框架的应用，大部分Spring Boot应用只需要非常少的配置就可以快速运行起来，是一个与微服务(MicroServices)相当契合的微框架。
网络上关于Spring Boot的QuickStart式中文内容已经相当丰富，但是对于部署后怎样便捷、安全地停止服务(shutdown)，还比较缺乏，最近发现Spring Boot的官方指南更新了相关内容，因此结合该部分更新，对如何**基于官方提供的特性**正确地停止Spring Boot应用进行简单说明。

主要有两种方式：通过`HTTP`发送`shutdown`信号，或者通过`service stop`的方式
<!--more-->
## 方式一：通过`HTTP`发送`shutdown`信号
该方式主要依赖`Spring Boot Actuator`的`endpoint`特性，具体步骤如下：

### 1. 在`pom.xml`中引入`actuator`依赖
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
### 2. 开启`shutdown endpoint`
  `Spring Boot Actuator`的`shutdown endpoin`t默认是关闭的，因此在`application.properties`中开启`shutdown endpoint`：
```
#启用shutdown
endpoints.shutdown.enabled=true
#禁用密码验证
endpoints.shutdown.sensitive=false
```
### 3. 发送`shutdown`信号
  `shutdown`的默认`url`为`host:port/shutdown`，当需要停止服务时，向服务器`post`该请求即可，如：
`curl -X POST host:port/shutdown`
将得到形如`{"message":"Shutting down, bye..."}`的响应

### 4. 安全设置
可以看出，使用该方法可以非常方便的进行远程操作，但是需要注意的是，正式使用时，必须对该请求进行必要的安全设置，比如借助`spring-boot-starter-security`进行身份认证：
  1. pom.xml添加security依赖
    ```
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    ```
  2. 开启安全验证
    在`application.properties`中变更配置,并
    ```
    #开启shutdown的安全验证
    endpoints.shutdown.sensitive=true
    #验证用户名
    security.user.name=admin
    #验证密码
    security.user.password=secret
    #角色
    management.security.role=SUPERUSER
    ```
  3. 指定路径、IP、端口
    ```
    #指定shutdown endpoint的路径
    endpoints.shutdown.path=/custompath
    #也可以统一指定所有endpoints的路径`management.context-path=/manage`
    #指定管理端口和IP
    management.port=8081
    management.address=127.0.0.1
    ```


## 方式二：部署为Unix/Linux Service
该方式主要借助官方的`spring-boot-maven-plugin`创建"Fully executable" jar ，这中jar包内置一个shell脚本，可以方便的将该应用设置为Unix/Linux的系统服务(init.d service),官方对该功能在CentOS和Ubuntu进行了测试，对于OS X和FreeBSD,可能需要自定义。具体步骤如下:
### 1. 在`pom.xml`中引入插件：
  ```
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <configuration>
    <executable>true</executable>
  </configuration>
</plugin>
  ```

### 2. 设置为系统服务
  将你的应用打成jar包，部署到服务器，假设部署路径为/var/app，包名为app.jar，通过如下方式将应该设置为一个系统服务：
`sudo ln -s /var/app/app.jar /etc/init.d/app`

### 3. 赋予可执行权限：
`chmod u+x app.jar`

### 4. 以系统服务的方式管理
  接下来，就可以使用我们熟悉的service foo start|stop|restart来对应用进行启停等管理了
`sudo service app start|stop`
命令将得到形如`Started|Stopped [PID]`的结果反馈

默认PID文件路径：/var/run/appname/appname.pid
默认日志文件路径：/var/log/appname.log

这可能是我们更熟悉也更常用的管理方式。

### 自定义参数
在这种方式下，我们还可以使用自定义的.conf文件来变更默认配置，方法如下：
1. 在jar包相同路径下创建一个.conf文件，名称应该与.jar的名称相同，如appname.conf
2. 在其中配置相关变量，如：
```
JAVA_HOME=/usr/local/jdk
JAVA_OPTS=-Xmx1024M
LOG_FOLDER=/custom/log
```

### 安全设置
作为应用服务，安全性是一个不能忽略的问题，如下一些操作可以作为部分基础设置参考：
- 为服务创建一个独立的用户，同时最好将该用户的shell绑定为/usr/sbin/nologin
- 赋予最小范围权限：`chmod 500 app.jar`
- 阻止修改：`sudo chattr +i app.jar`
- 对.conf文件做类似的工作：`chmod 400 app.conf`,`sudo chown root:root app.conf`

## References:
1. [Installing Spring Boot applications](http://docs.spring.io/spring-boot/docs/current/reference/html/deployment-install.html)
2. [Endpoints](http://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-enabling.html)
3. [Securing sensitive endpoints](http://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-monitoring.html#production-ready-sensitive-endpoints)
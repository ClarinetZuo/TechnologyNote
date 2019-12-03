# SpringBoot
## SpringBoot是一个微服务框架，可以快速开发一个Spring项目，两个重要的核心思想是Starter(启动器)和自动配置；即使用Boot开发项目，可以做到零配置
## 微服务
### 微小的功能单元，将一个大的系统拆分出多个彼此独立的服务，多个微服务之间可以协同工作
## SpringBoot的特点
- 独立应用，编码简单
- 导包容易(Starter的存在)
- 配置容易(自动配置，不需要写xml)
- 无需打包war，内嵌应用服务器(Tomcat)
## 关于pom.xml
### <!--parent>:继承关系，boot将所有jar的版本在此标记中定义好了，不需要指定版本号
### <!--dependencies>:依赖，boot中可以使用启动器来指定，每个启动器都是一个应用场景，每个启动器中包含了这个场景所需要的所有jar包；指定了启动器，就不需要分别导jar包了
## SpringBoot常用注解
### @SpringBootApplication
- 代表被注解的类是boot的配置类，是boot应用的入口；这个注解主要由三个注解组成
  - @SpringBootConfiguration：boot配置类
  - @EnableAutoConfiguration：开启自动扫描包
  - @ComponentScan:扫描包
## Spring的三种配置方式
### xml < bean id="" class="">
### Annotation;@Component @Autowired
### Java配置
```Java
@Configuration
public class JavaConfig {
// 由容器来创建对象
@Bean
public UserService getUserService(){
	return new UserService();
}
}
```
## SpringBoot的目录结构
### 多了一个resources包
- static：静态文件
- template：模板 thymeleaf
- application.properties
## SpringBoot的配置文件
### 属性文件(application.properties)
### yml(yaml)文件
#### 文件格式：键值对，注意空格
```yml
person:
  name: 左俊皓
  age: 25
  hobbies: ["干","起","来"] // 数组
  birth: 1994/6/10 // 日期
  map: {"java":100,"Hadoop":100} // map
```
### 读取配置文件的两种方式
- @Value(): "${}" 等等
- @ConfigurationProperties(prefix="xxx")
## SpringBoot中的多配置文件
### 可以在resources包下增加不同的properties配置文件，配置文件的名称是application-[prod].properties，然后再application.properties文件中，spring.profiles.active=prod，来激活对应的配置文件
### 可以使用yml文档块的方式
```yml
spring:
  profiles:
    active: dev
---
spring:
  profiles: dev
server:
  port: 8888
  servlet:
    context-path: /dev
---
spring:
  profiles: test
server:
  port: 9999
  servlet:
    context-path: 9999
```
## SpringBoot的模板引擎
### 根据模板和数据生成指定的文件
### 常见的模板引擎有
- Velocity
- FreeMarker
- Thymeleaf
### 使用Thymeleaf的步骤
- 导入Thymeleaf启动器
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
- 在resources/templates/目录中添加模板(success.html...)
```html
<!DOCTYPE html>
<!-- 导入命名空间-->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form>
        <input type="text" th:value="${name}"/><br/>
        <input type="text" th:value="${age}"/><br/>
        <input type="text" th:value="${sex}"/><br/>
    </form>
</body>
</html>
```
- 在Controller中返回一个代表模板名称的字符串
```Java
package com.example.webdemo.mvc;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.Arrays;
import java.util.Map;

/**
 * author:Zuo Junhao
 * NEFU
 */
@Controller
public class TestHandler {
    @RequestMapping("/show")
    public String showInfo(Map<String,Object> map){
        map.put("name","zjh");
        map.put("age","25");
        map.put("sex","male");
        map.put("hobby", Arrays.asList("play","fuck"));
        return "show";
    }
}
```
### Thymeleaf的语法
#### 简单表达式
- ${...}
- *{...}
- #{...}
- @{...}
- ~{...}
## 开发RESTful风格的CURD

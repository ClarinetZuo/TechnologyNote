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
  - @ComponentScan:
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

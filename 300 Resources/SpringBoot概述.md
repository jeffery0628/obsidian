---
Create: 2021年 十一月 29日, 星期一 09:57
tags: 
  - Engineering/java/spring-boot
  - 大数据
---

# 概述
Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。 
该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。
通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域(rapid application development)成为领导者。

Spring框架由于其繁琐的配置，一度被人认为“配置地狱”，各种XML、Annotation配置混合使用，让人眼花缭乱，而且如果出错了也很难找出原因。 
通过SpringMVC框架部署和发布web程序，需要和系统外服务器进行关联，操作繁琐不方便。 
Spring Boot是由Spring官方推出的一个新框架，对Spring进行了高度封装，是Spring未来的发展方向。使用Spring Boot框架后，可以帮助开发者快速搭建Spring框架，也可以帮助开发者快速启动一个Web服务，无须依赖外部Servlet容器，使编码变得简单，使配置变得简单，使部署变得简单，使监控变得简单。

## spring 的发展历史
1.	Spring1.x 时代 
	在Spring1.x时代，都是通过xml文件配置bean
	随着项目的不断扩大，需要将xml配置分放到不同的配置文件中
	需要频繁的在java类和xml配置文件中切换。
2.	Spring2.x时代 
	随着JDK 1.5带来的注解支持，Spring2.x可以使用注解对Bean进行申明和注入，大大的	减少了xml配置文件，同时也大大简化了项目的开发。 
	那么，问题来了，究竟是应该使用xml还是注解呢？
	>最佳实践：
	>应用的基本配置用xml，比如：数据源、资源文件等； 
	>业务开发用注解，比如：Service中注入bean等； 
3. Spring3.x到Spring4.x 
	从Spring3.x开始提供了Java配置方式，使用Java配置方式可以更好的理解你配置的Bean，现在我们就处于这个时代，并且Spring4.x和Spring boot都推荐使用java配置的式。 

```

Spring 1.X
使用基本的框架类及配置文件（.xml）实现对象的声明及对象关系的整合。
org.springframework.core.io.ClassPathResource
org.springframework.beans.factory.xml.XmlBeanFactory
org.springframework.context.support.ClassPathXmlApplicationContext 
Spring 2.X
使用注解代替配置文件中对象的声明。简化配置。
org.springframework.stereotype.@Component
org.springframework.stereotype.@Controller
org.springframework.stereotype.@Service
org.springframework.stereotype.@Repository
org.springframework.stereotype.@Scope
org.springframework.beans.factory.annotation.@Autowired 
Spring 3.X
使用更强大的注解完全代替配置文件。
org.springframework.context.annotation.AnnotationConfigApplicationContext
org.springframework.context.annotation.@Configuration
org.springframework.context.annotation.@Bean
org.springframework.context.annotation.@Value
org.springframework.context.annotation.@Import 
Spring 4.X
使用条件注解强化之前版本的注解。
org.springframework.context.annotation.@Conditional 

```


---
Create: 2021年 十一月 28日, 星期日 23:12
tags: 
  - Engineering/java/spring
  - 大数据
---
#TODO 

# 概述
1. Spring是一个开源框架 。
2. Spring为简化企业级开发而生，使用Spring，JavaBesan就可以实现很多以前要靠EJB才能实现的功能。同样的功能，在EJB中要通过繁琐的配置和复杂的代码才能够实现，而在Spring中却非常的优雅和简洁。
3. Spring是一个**IOC**(DI)和**AOP**容器框架。
4. Spring的优良特性：
	1. **非侵入式**：基于Spring开发的应用中的对象可以不依赖于Spring的API
	2. **依赖注入**：DI——Dependency Injection，反转控制(IOC)最经典的实现。
	3.  **面向切面编程**：Aspect Oriented Programming——AOP
	4.   **容器**：Spring是一个容器，因为它包含并且管理应用对象的生命周期
	5.   组件化**：Spring实现了使用简单的组件配置组合成一个复杂的应用。在 Spring 中可以使用XML和Java注解组合这些对象。
	6.    **一站式**：在IOC和AOP的基础上可以整合各种企业应用的开源框架和优秀的第三方类库（实际上Spring 自身也提供了表述层的SpringMVC和持久层的Spring JDBC）。
5.    Spring模块
   ![[700 Attachments/Pasted image 20211128231643.png]]
   
# Spring HelloWorld
1. 创建一个Maven版的Java工程
2. 在pom.xml中加入对Spring的依赖

	```
	<dependency>
		  <groupId>org.springframework</groupId>
		  <artifactId>spring-context</artifactId>
		  <version>4.0.0.RELEASE</version>
	</dependency>

	```
3. 创建Spring的核心配置文件:applicationContext.xml



## 常用注解标识组件:
1. 普通组件：@Component  ,标识一个受Spring IOC容器管理的组件
2. 持久化层组件：@Repository , 标识一个受Spring IOC容器管理的持久化层组件
3. 业务逻辑层组件：@Service  ,标识一个受Spring IOC容器管理的业务逻辑层组件
4. 表述层控制器组件：@Controller , 标识一个受Spring IOC容器管理的表述层控制器组件

## 组件命名规则
1. 默认情况：使用组件的简单类名首字母小写后得到的字符串作为bean的id
2. 使用组件注解的value属性指定bean的id

> 注意：事实上Spring并没有能力识别一个组件到底是不是它所标记的类型，即使将@Respository注解用在一个表述层控制器组件上面也不会产生任何错误，所以 @Respository、@Service、@Controller这几个注解仅仅是为了让开发人员自己 明确当前的组件扮演的角色。

## @Autowired注解 
1. 根据类型实现自动装配。
2. 构造器、普通字段(即使是非public)、一切具有参数的方法都可以应用@Autowired 注解
3. 默认情况下，所有使用@Autowired注解的属性都需要被设置。当Spring找不到匹配的bean装配属性时，会抛出异常。
4. 若某一属性允许不被设置，可以设置@Autowired注解的required属性为 false
5. 默认情况下，当IOC容器里存在多个类型兼容的bean时，Spring会尝试匹配bean 的id值是否与变量名相同，如果相同则进行装配。如果bean的id值不相同，通过类型的自动装配将无法工作。此时可以在@Qualifier注解里提供bean的名称。Spring甚至允许在方法的形参上标注@Qualifiter注解以指定注入bean的名称。
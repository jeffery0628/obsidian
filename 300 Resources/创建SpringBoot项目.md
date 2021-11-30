---
Create: 2021年 十一月 29日, 星期一 22:10
tags: 
  - Engineering/java/spring-boot
  - 大数据
---

# 创建SpringBoot项目
## 自动创建一个SpringBoot项目
通过 STS 快速创建一个Spring Boot项目,运行查看结果: Spring Banner

## 手动创建一个SpringBoot 项目
1. 创建Maven项目 ： 修改项目中的pom.xml文件，设置JDK编译版本为1.8 
	```xml
	<project>
		...
		<build>
			<plugins>
				<!-- 修改maven默认的JRE编译版本，1.8代表JRE编译的版本，根据自己的安装版本选择1.7或1.8 -->
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-compiler-plugin</artifactId>
					<configuration>
						<source>1.8</source>
						<target>1.8</target>
					</configuration>
				</plugin>
			</plugins>
		</build>
		...
	</project>

	```
	>SSM基础架构中，需要生成web.xml文件，Spring Boot框架中为什么没有？
	>Spring Boot框架开发web系统，是基于servlet3.0或以上规范，无需web.xml文件

2. 集成Spring Boot框架:修改pom.xml文件，增加Spring Boot框架的依赖关系及对Web环境的支持
	```xml
	<project>
    ...
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.8.RELEASE</version>
    </parent>
    ...
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    ...
	</project>

	```
	
	> 以往的项目中，所有类库的依赖关系都需要我们自己导入到pom.xml文件中，但是Spring Boot项目增加spring-boot-starter-web依赖后，会自动加载web环境配置相关依赖(SpringMVC, Tomcat)，简化了我们的操作。
	> spring-boot-starter-parent：继承Spring Boot的相关参数 
	> spring-boot-starter-xxx：代表一个Spring Boot模块(参考附录1.Spring Boot相关模块) 
	> spring-boot-starter-web：代表Web模块，在这个模块中包含了许多依赖的JAR包 
3. 增加程序代码
	1. 集成环境,启动服务器
	2. 在src/main/java目录中增加类com.atguigu.crowdfunding.AtCrowdfundingApplication，并增加相应代码。 

	```java
		package com.atguigu.crowdfunding;
		import org.springframework.boot.SpringApplication;
		import org.springframework.boot.autoconfigure.SpringBootApplication;

		@SpringBootApplication
		public class AtCrowdfundingApplication { 
			//有网的时候可以自动创建,没有网的时候,我们可以手动创建.
			public static void main(String[] args) {
					SpringApplication.run(AtCrowdfundingApplication.class, args);
			}
		}
	```
	
	
	> Spring Boot项目中都会有一个以Application结尾的应用类，然后有一个标准的Java入口方法main方法。通过这个方法启动Spring Boot项目，方法中无需放入任何业务逻辑。 
	> @SpringBootApplication注解是Spring Boot核心注解
	> 右键点击项目或项目中的AtCrowdfundingApplication类, 选择菜单Run as Spring Boot App，控制台出现下面内容表示服务启动成功。 

4. 集成了Tomcat服务器
	当增加Web依赖后执行main方法，等同于启动Tomcat服务器, 默认端口号为8080。如果想要修改默认的Tomcat服务器端口号，可以通过全局配置文件进行配置，在src/main/resources/目录中增加application.properties文件。Spring Boot会自动读取src/main/resources/路径或类路径下/config路径中的application.properties文件或application.yml文件。
	```java
	server.context-path=/
	server.port=80
	server.session.timeout=60
	server.tomcat.max-threads=800
	server.tomcat.uri-encoding=UTF-8
	```
	





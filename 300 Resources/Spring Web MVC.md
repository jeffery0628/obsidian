---
Create: 2021年 十一月 28日, 星期日 23:24
tags: 
  - Engineering/java/spring-web-mvc
  - 大数据
---
#TODO 

# 概述
1. 一种轻量级的、基于MVC的Web层应用框架。偏前端而不是基于业务逻辑层。Spring框架的一个后续产品。Spring 为展现层提供的基于 MVC 设计理念的优秀的 Web 框架，是目前最主流的MVC 框架之一
2. Spring3.0 后全面超越 Struts2，成为最优秀的 MVC 框架
3. Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请求的控制器，而无须实现任何接口。
4. 支持 REST 风格的 URL 请求。
5.  采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性。

# 创建WEB工程
1.  创建一个Maven版的WEB工程
2.  在pom.xml中加入对Springmvc的依赖
	```
	<dependency>
		  <groupId>org.springframework</groupId>
		  <artifactId>spring-webmvc</artifactId>
		  <version>4.0.0.RELEASE</version>
	</dependency>

	```

3. 在web.xml中配置前端控制器DispatcherServlet
	```
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
			<init-param>
				 <param-name>contextConfigLocation</param-name>
				<param-value>classpath:springmvc.xml</param-value>
			</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>


	```
4. 创建Springmvc的核心配置文件
	```
	<!-- 组件扫描 -->
	<context:component-scan base-package="基本包 "></context:component-scan>

	<!-- 视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>


	```
5. 编写index.jsp页面，通过浏览器端发送请求
	```
	<a href="hello">Hello Springmvc </a>
	```

6. 编写请求处理器
	```
	/**
	 * 请求处理器
	 */
	@Controller
	public class HelloWorldHandler {

	}
	```
7. 编写请求处理方法，处理客户端的请求
```
/**
* 请求处理方法
* 浏览器端: http://localhost:8080/Springmvc01/hello
* @RequestMapping: 请求映射. 指定哪个请求交给哪个方法处理.
*/
	@RequestMapping(value="/hello")
	public String  handleHello() {
		System.out.println("Hello Springmvc .");
		return "success";
	}

```

8. 编写视图，呈现结果,根据视图解析器中prefix中的配置，在WEB-INF目录下创建views目录，并在views目录中创建success.jsp页面.
9. 将工程部署到Tomcat中，并启动Tomcat
10. 浏览器端发送请求进行访问

# 请求参数
@RequestParam 处理请求参数

# 响应数据
ModelAndView  Map  Model  处理响应数据

# REST
1. RESTful  URL
2. @PathVariable 
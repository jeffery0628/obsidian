---
Create: 2021年 十一月 29日, 星期一 22:12
tags: 
  - Engineering/java/spring-boot
  - 大数据
---

前提：已经配置好SpringBoot环境，现在需要配置Spring框架及SpringMVC框架的业务环境 
# SpringBoot 集成 Spring & Spring Web MVC
1. @ComponentScan注解
	```java
	package com.atguigu.crowdfunding;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.context.annotation.ComponentScan;
	@ComponentScan(basePackages="com.atguigu") 
	@SpringBootApplication
	public class AtCrowdfundingApplication {
			public static void main(String[] args) {
					SpringApplication.run(AtCrowdfundingApplication.class, args);
			}
	}

	```

2. 默认扫描：默认扫描当前包com.atguigu.crowdfunding和子包com.atguigu.crowdfunding.*
如果还需要扫描其他的包，那么需要增加@ComponentScan注解,指定包名进行扫描。
3. 增加控制器代码：在src/main/java目录中增加类com.atguigu.crowdfunding.controller.MemberController，并增加相应代码。
	```java
	package com.atguigu.crowdfunding.controller;
	import java.util.HashMap;
	import java.util.Map;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.ResponseBody;
	@Controller
	@RequestMapping("/member")
	public class MemberController {
			@ResponseBody
			@RequestMapping("/index")
			public Object index() {
					Map map = new HashMap();
					map.put("username", "张三");
					return map;
			}
	}
	```
4. 执行main方法启动应用:访问路径http://127.0.0.1:8080[/应用路径名称]/member/index 页面打印JSON字符串即可

5. @Controller和@RestController区别:
	官方文档：@RestController is a stereotype annotation that combines @ResponseBody and @Controller.表示@RestController等同于@Controller + @ResponseBody，所以上面的代码可以变为：
	```java
	package com.atguigu.crowdfunding.controller;

	import java.util.HashMap;  
	import java.util.Map;

	import org.springframework.web.bind.annotation.RequestMapping;  
	import org.springframework.web.bind.annotation.RestController;

	@RestController  
	@RequestMapping("/member")  
	public class MemberController {

		@RequestMapping("/index")  
		public Object index() {  
				Map map = new HashMap();  
				map.put("username", "张三");  
				return map; //JSON => JavaScript Object Notation

		}

	}

	```

6. 增加服务层代码:Service接口，ServiceImpl实现类的使用和SSM架构中的使用方式完全相同。
	```java
	package com.atguigu.crowdfunding.service;
 
	public interface MemberService {

	}
	
	
	package com.atguigu.crowdfunding.service.impl;
	import org.springframework.stereotype.Service; 
	import com.atguigu.crowdfunding.service.MemberService;

	@Service
	public class MemberServiceImpl implements MemberService {
	}

	@RestController
	public class MemberController {
		@Autowired
		private MemberService memberService ;
	}

	
	```

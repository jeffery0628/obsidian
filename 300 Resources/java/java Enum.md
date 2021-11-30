---
Create: 2021年 十月 28日, 星期四 13:08
tags: 
  - Engineering/java
  - 大数据
---

# 枚举

类的对象只有有限个的、确定的

```java
enum Season{
	SPRING,SUMMER,AUTUMN,WINTER;
}
```

> 1. 使用 enum 定义的枚举类默认继承了 java.lang.Enum类。因此不能再继承其他类。
> 2. 枚举类的所有构造器只能使用 private 访问控制符
> 3. 枚举类的所有实例必须在枚举类中显式列出(, 分隔   ; 结尾)，必须在枚举类的第一行声明枚举类对象。列出的实例系统会自动添加 public static final 修饰
> 4. JDK 1.5 之后可以在 switch 表达式中使用Enum定义的枚举类的对象作为表达式, case 子句可以直接使用枚举值的名字

## 特殊说明

1. 枚举中定义属性

	```java
	public class TestWeekField {
		public static void main(String[] args) {
			Week w = Week.MONDAY;
			System.out.println(w);
		}
	}
	enum Week{
		MONDAY("星期一"),
		TUESDAY("星期二"),
		WEDNESDAY("星期三"),
		THURSDAY("星期四"),
		FRIDAY("星期五"),
		SATURDAY("星期六"),
		SUNDAY("星期日");
	    
		private final String DESCRPTION;
	    
		private Week(String dESCRPTION) {
			DESCRPTION = dESCRPTION;
		}
		public String toString(){
			return DESCRPTION;
		}
	
	```

2. 枚举类可以自定义方法，静态和非静态

3. 枚举类可以实现一个或者多个接口

	若每个枚举值在调用实现的接口方法呈现相同的行为方式，则只要统一实现该方法即可。

	若需要每个枚举值在调用实现的接口方法呈现出不同的行为方式, 则可以让每个枚举值分别来实现该方法

	```java
	interface Change{
		void degenerate();
	}
	interface Checkable{
		void check();
	}
	enum Gender implements Change,Checkable{
	    
		MAN{
			public void degenerate(){
				System.out.println("咔嚓一刀");
			}
		},
	    WOMAN{
			public void degenerate(){
				System.out.println("比较复杂");
			}
		};
		public void check(){
			System.out.println("脱光");
		}
	}
	
	```

4. 枚举类可以自己定义抽象方法

	```java
	enum Payment{
		CASH{
			public void pay(){
				System.out.println("现金支付");
			}
		},
		WECHAT{
			public void pay(){
				System.out.println("微信支付");
			}
		},
		ALIPAY{
			public void pay(){
				System.out.println("支付鸨支付");
			}
		},
		CARD{
			public void pay(){
				System.out.println("银行卡支付");
			}
		},
		CREDIT{
			public void pay(){
				System.out.println("信用卡支付");
			}
		};
		public abstract void pay();
	}
	```

	

	## 拓展

	java.util.EnumSet和java.util.EnumMap是两个枚举集合。

	1. EnumSet保证集合中的元素不重复；
	2. EnumMap中的key是enum类型，而value则可以是任意类型。



[[200 Areas/230 Engineering/231 java|java 目录]]



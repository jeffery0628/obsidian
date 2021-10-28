---
Create: 2021年 十月 28日, 星期四 09:51
tags: 
  - Engineering/java
  - 大数据
---

# java.time

如果我们可以跟别人说：“我们在1502643933071见面，别晚了！”那么就再简单不过了。但是我们希望时间与昼夜和四季有关，于是事情就变复杂了。

Java1.0中包含了一个Date类，但是它的大多数方法已经在Java 1.1引入Calendar类之后被弃用了。而Calendar并不比Date好多少。它们面临的问题是：

- 可变性：像日期和时间这样的类应该是不可变的。Calendar类中可以使用三种方法更改日历字段：`set()`、`add() `和 `roll()`。
- 偏移性：Date中的年份是从1900开始的，而月份都是从0开始的。
- 格式化：格式化只对Date有用，Calendar则不行。
- 此外，它们也不是线程安全的，不能处理闰秒等。

Java 8 吸收了 Joda-Time 的精华，以一个新的开始为 Java 创建优秀的 API。

- java.time – 包含值对象的基础包
- java.time.chrono – 提供对不同的日历系统的访问。
- java.time.format – 格式化和解析时间和日期
- java.time.temporal – 包括底层框架和扩展特性
- java.time.zone – 包含时区支持的类

新的 java.time 中包含了所有关于时钟（Clock），本地日期（`LocalDate`）、本地时间（`LocalTime`）、本地日期时间（`LocalDateTime`）、时区（`ZonedDateTime`）和持续时间（`Duration`）的类。

历史悠久的 Date 类新增了 toInstant() 方法，用于把 Date 转换成新的表示形式。这些新增的本地化时间日期 API 大大简化了了日期时间和本地化的管理。

## 常用方法

|                                                              | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| now() / now(ZoneId zone)                                     | 静态方法，根据当前时间创建对象/指定时区的对象                |
| of()                                                         | 静态方法，根据指定日期/时间创建对象                          |
| getDayOfMonth()/getDayOfYear()                               | 获得月份天数(1-31) /获得年份天数(1-366)                      |
| getDayOfWeek()                                               | 获得星期几(返回一个 DayOfWeek 枚举值)                        |
| getMonth()                                                   | 获得月份, 返回一个 Month 枚举值                              |
| getMonthValue() / getYear()                                  | 获得月份(1-12) /获得年份                                     |
| getHours()/getMinute()/getSecond()                           | 获得当前对象对应的小时、分钟、秒                             |
| withDayOfMonth()/withDayOfYear()/withMonth()/withYear()      | 将月份天数、年份天数、月份、年份修改为指定的值并返回新的对象 |
| with(TemporalAdjuster t)                                     | 将当前日期时间设置为校对器指定的日期时间                     |
| plusDays(), plusWeeks(), plusMonths(), plusYears(),plusHours() | 向当前对象添加几天、几周、几个月、几年、几小时               |
| minusMonths() / minusWeeks()/minusDays()/minusYears()/minusHours() | 从当前对象减去几月、几周、几天、几年、几小时                 |
| plus(TemporalAmount t)/minus(TemporalAmount t)               | 添加或减少一个 Duration 或 Period                            |
| isBefore()/isAfter()                                         | 比较两个 LocalDate                                           |
| isLeapYear()                                                 | 判断是否是闰年（在LocalDate类中声明）                        |
| format(DateTimeFormatter t)                                  | 格式化本地日期、时间，返回一个字符串                         |
| parse(Charsequence text)                                     | 将指定格式的字符串解析为日期、时间                           |



```java
public static void main(String[] args) {

    LocalDate date = LocalDate.now();  // 本地日期
    LocalTime time = LocalTime.now();  // 本地时间
    LocalDateTime datetime = LocalDateTime.now(); // 本地日期时间
    System.out.println(date+" "+time+" "+datetime);  // 2021-09-04 21:37:12.854 2021-09-04T21:37:12.854

    // LocalDate date = LocalDate.now();
    // LocalDate date = LocalDate.of(2017, 3, 20);
    LocalDate date1 = LocalDate.parse("2017-03-12");
    System.out.println(date1);

    // 本地日期时间 的 常用函数
    LocalDateTime t = LocalDateTime.now();
    System.out.println("这一天是这一年的第几天："+t.getDayOfYear());
    System.out.println("年："+t.getYear());
    System.out.println("月："+t.getMonth());
    System.out.println("月份值："+t.getMonthValue());
    System.out.println("日："+t.getDayOfMonth());
    System.out.println("星期："+t.getDayOfWeek());
    System.out.println("时："+t.getHour());
    System.out.println("分："+t.getMinute());
    System.out.println("秒："+t.getSecond());
    System.out.println(t.getMonthValue());


    // 本地日期常用函数
    LocalDate date2 = date.with(TemporalAdjusters.firstDayOfMonth());// 获取这个月的第一天
    System.out.println(date2);  // 2021-09-01

    // 获取这个月的最后一天
    LocalDate date3 = date.with(TemporalAdjusters.lastDayOfMonth());
    System.out.println(date3);  //  2021-09-30

    //45天后的日期
    LocalDate date4 = date.plusDays(45);
    System.out.println(date4);  //  2021-10-19

    //20天前的日期
    LocalDate date5 = date.minusDays(20);
    System.out.println(date5);  //  2021-08-15

    boolean before = date.isBefore(date5);
    System.out.println(date+"是否比"+date5+"早" + before);  //  2021-09-04是否比2021-08-15早false
    System.out.println(date+"是否是闰年："+date.isLeapYear());  //  2021-09-04是否是闰年：false

    MonthDay month = MonthDay.of(6, 28);
    MonthDay today = MonthDay.from(date);
    System.out.println("今天是否是生日：" + month.equals(today));  // 今天是否是生日：false

}
```



## Instant

时间线上的一个瞬时点。 这可能被用来记录应用程序中的事件时间戳。

在处理时间和日期的时候，我们通常会想到年,月,日,时,分,秒。然而，这只是时间的一个模型，是面向人类的。第二种通用模型是面向机器的，或者说是连续的。在此模型中，时间线中的一个点表示为一个很大的数，这有利于计算机处理。在UNIX中，这个数从1970年开始，以秒为的单位；同样的，在Java中，也是从1970年开始，但以毫秒为单位。



java.time包通过值类型Instant提供机器视图。Instant表示时间线上的一点，而不需要任何上下文信息，例如，时区。概念上讲，它只是简单的表示自1970年1月1日0时0分0秒（UTC）开始的秒数。因为java.time包是基于纳秒计算的，所以Instant的精度可以达到纳秒级。

### 常用方法

| 方法                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| now()                         | 静态方法，返回默认UTC时区的Instant类的对象。                 |
| ofEpochMilli(long epochMilli) | 静态方法，返回在1970-01-01 00：00：00基础上加上指定毫秒数之后的Instance类的对象。 |
| atOffset(ZoneOffset offset)   | 结合即时的偏移来创建一个OffsetDateTime。                     |
| toEpochMilli()                | 返回1970-01-01 00：00：00 到当前时间的毫秒数，即为时间戳。   |

> 时间戳：指格林威治时间1970年01月01日00时00分00秒（北京时间1970年01月01日08时00分00秒）起至现在的总秒数。

```java
public static void main(String[] args) {

    Instant instant = Instant.now();
    System.out.println(instant);  // 2021-09-04T13:55:15.943Z  本地时间减去8个小时

    //偏移8个小时
    OffsetDateTime atOffset = instant.atOffset(ZoneOffset.ofHours(8));
    System.out.println(atOffset);   // 2021-09-04T21:55:15.943+08:00

    long milli = instant.toEpochMilli();
    System.out.println(milli);  // 时间戳：1630763715943

    Instant in2 = Instant.ofEpochSecond(10000000);
    System.out.println(in2);  // 1970-04-26T17:46:40Z

}
```





## ZonedDateTime

如果不用去处理时区和它带来的复杂性，那是幸运的。java.time包下的LocalDate、LocalTime、LocalDateTime和Instant基本能满足需求。

当不可避免时区时，ZonedDateTime等类可以满足我们的需求。ZonedDateTime：一个在ISO-8601日历系统时区的日期时间，如 2007-12-03T10:15:30+01:00 Europe/Paris。

- 其中每个时区都对应着ID，地区ID都为“{区域}/{城市}”的格式，例如：Asia/Shanghai等
- now()：使用系统时间获取当前的ZonedDateTime
- now(ZoneId)：返回指定时区的ZonedDateTime

## ZoneId

该类中包含了所有的时区信息，一个时区的ID，如 Europe/Paris：

- getAvailableZoneIds()：静态方法，可以获取所有时区信息
- of(String id)：静态方法，用指定的时区信息获取ZoneId对象

## Clock

使用时区提供对当前即时、日期和时间的访问的时钟。

```java
public static void main(String[] args) {
    Set<String> availableZoneIds = ZoneId.getAvailableZoneIds();
    for (String string : availableZoneIds) {
        System.out.println(string);
    }

    ZonedDateTime t = ZonedDateTime.now();  // 2021-09-04T22:02:37.991+08:00[Asia/Shanghai]
    System.out.println(t);

    ZonedDateTime t1 = ZonedDateTime.now(ZoneId.of("America/New_York"));
    System.out.println(t1);     // 2021-09-04T10:02:37.993-04:00[America/New_York]

    // Clock clock = Clock.systemDefaultZone();
    Clock c = Clock.system(ZoneId.of("America/New_York"));
    System.out.println(c.getZone());  //  America/New_York
    System.out.println(c.instant());  //  2021-09-04T14:02:37.999Z

}
```



## Duration

用于计算两个“时间”间隔

```java
public static void main(String[] args) {
    LocalDateTime t1 = LocalDateTime.now();
    LocalDateTime t2 = LocalDateTime.of(2021, 10, 1, 0, 0, 0, 0);
    Duration between = Duration.between(t1, t2);
    System.out.println(between);  // PT625H54M30.941S

    System.out.println("相差的总天数："+between.toDays());  // 相差的总天数：26
    System.out.println("相差的总小时数："+between.toHours());  // 相差的总小时数：625
    System.out.println("相差的总分钟数："+between.toMinutes());  // 相差的总分钟数：37554
    System.out.println("相差的总秒数："+between.getSeconds());  // 相差的总秒数：2253270
    System.out.println("相差的总毫秒数："+between.toMillis());  // 相差的总毫秒数：2253270941
    System.out.println("相差的总纳秒数："+between.toNanos());  // 相差的总纳秒数：2253270941000000
    System.out.println("不够一秒的纳秒数："+between.getNano());  // 不够一秒的纳秒数：941000000

}
```



## Period

用于计算两个“日期”间隔

```java
public static void main(String[] args) {
    LocalDate t1 = LocalDate.now();
    LocalDate t2 = LocalDate.of(2021, 10, 2);
    Period between = Period.between(t1, t2);
    System.out.println(between);  // P28D

    System.out.println("相差的年数："+between.getYears());  // 相差的年数：0
    System.out.println("相差的月数："+between.getMonths());  // 相差的月数：0
    System.out.println("相差的天数："+between.getDays());  //相差的天数：28
    System.out.println("相差的总数："+between.toTotalMonths());  //相差的总数：0

}
```





## 与传统日期处理的转换

| 类                                                       | To 遗留类                             | From 遗留类                 |
| -------------------------------------------------------- | ------------------------------------- | --------------------------- |
| java.time.Instant与java.util.Date                        | Date.from(instant)                    | date.toInstant()            |
| java.time.Instant与java.sql.Timestamp                    | Timestamp.from(instant)               | timestamp.toInstant()       |
| java.time.ZonedDateTime与java.util.GregorianCalendar     | GregorianCalendar.from(zonedDateTime) | cal.toZonedDateTime()       |
| java.time.LocalDate与java.sql.Time                       | Date.valueOf(localDate)               | date.toLocalDate()          |
| java.time.LocalTime与java.sql.Time                       | Date.valueOf(localDate)               | date.toLocalTime()          |
| java.time.LocalDateTime与java.sql.Timestamp              | Timestamp.valueOf(localDateTime)      | timestamp.toLocalDateTime() |
| java.time.ZoneId与java.util.TimeZone                     | Timezone.getTimeZone(id)              | timeZone.toZoneId()         |
| java.time.format.DateTimeFormatter与java.text.DateFormat | formatter.toFormat()                  | 无                          |




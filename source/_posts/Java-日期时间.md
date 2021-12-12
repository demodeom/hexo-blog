---
title: Java 日期时间
date: 2021-11-12 02:44:09
tags:
	- Java 日期时间
categories:
	- Java 基础
---

Java 提供很多时间相关的类， 比如： Date、 Time、 LocalDate、 LocalTime、LocalDateTime 等等, 如何选择往往取决于用途

<!-- more -->


## LocalDate LocalTime LocalDateTime

LocalDate LocalTime LocalDateTime 遵循的是国际标准ISO 8601

### 日期

**获取当前日期**

```java
LocalDate ld = LocalDate.now();
```

可以分别获取 年 月 日

```java
int year = ld.getYear();
int month = ld.getMonth().getValue();
int day = ld.getDayOfMonth();
```

注: 月 日 不是从 0 开始， 1 月 就是 1, 不再是 0


**构建 LocalDate**

```java
LocalDate ld = LocalDate.parse("2020-12-09");
LocalDate ld_2 = LocalDate.parse("12-09-2020", DateTimeFormatter.ofPattern("MM-dd-yyyy"));
LocalDate ld_3 = LocalDate.of(2020, 12 , 9);
```

LocalDate 默认使用 ISO 8601 标准，也可以指定日期格式

### 时间

**获取本地时间**

```java
LocalTime lt = LocalTime.now();
```

可以分别获取 时 分 秒 纳秒

```java
int hour = lt.getHour();
int second = lt.getSecond();
int minute = lt.getMinute();
int nano = lt.getNano();
```

**构建指定本地时间**

```java
LocalTime lt = LocalTime.parse("13:24:30");
LocalTime lt_1 = LocalTime.of(13, 24, 30, 0);
LocalTime lt_2 = LocalTime.parse("13:24:30", DateTimeFormatter.ofPattern("H:i:s"));
```

### 日期时间

**获取指定本地时间**

```java
LocalDateTime ldt = LocalDateTime.now();
```

可以分别获取 年 月 日 时 分 秒 纳秒

```java
int year = ldt.getYear();
int month = ldt.getMonth().getValue();
int day = ldt.getDayOfMonth();
int hour = ldt.getHour();
int second = ldt.getSecond();
int minute = ldt.getMinute();
int nano = ldt.getNano();
```

**构建本地日期时间**

```java
LocalDateTime m_ldt_1 = LocalDateTime.of(2021, 6, 11, 23, 12, 24, 3000);

// 使用 LocalDate 和 LocalTime 构建
LocalDate mld = LocalDate.of(2021, 6, 11);
LocalTime mlt = LocalTime.of(23, 12, 24);
LocalDateTime m_ldt_2 = LocalDateTime.of(mld, mlt);

// 解析默认格式
LocalDateTime s_ldt_1 = LocalDateTime.parse("2021-12-13T23:12:24");
// 解析自定义格式
LocalDateTime s_ldt_2 = LocalDateTime.parse("2021-12-13 23:12:24", DateTimeFormatter.ofPattern("y-M-d H:m:s"));
```

## 时区

### ZoneId

获取当前时区

```java
ZoneId zoneId = ZoneId.systemDefault();

System.out.println(zoneId);
```

获取指定时区

```java
ZoneId zoneId = ZoneId.systemDefault("America/New_York");

System.out.println(zoneId);
```

### ZonedDateTime

获取当前时区的时期时间

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println("本地时区" + zonedDateTime);
// 本地时区 2021-11-26T08:34:05.919145+08:00[Asia/Shanghai]
```

获取于此同时， 纽约日期时间

```java
ZonedDateTime zonedDateTimeNewYork = ZonedDateTime.now(ZoneId.of("America/New_York"));
System.out.println("纽约时区" + zonedDateTimeNewYork);
// 纽约时区 2021-11-25T19:34:05.929306-05:00[America/New_York]
```

从一个时区转另一个时区

```java
LocalDateTime ldt = LocalDateTime.now();

ZonedDateTime zdt_local = ldt.atZone(ZoneId.systemDefault());
ZonedDateTime zdt_new_york = zdt_local.withZoneSameInstant(ZoneId.of("America/New_York"));
```


### ZonedDateTime转换

ZonedDateTime 转 long

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();
Long timestamps = zonedDateTime.toEpochSecond();
System.out.println("本地时间戳" + timestamps);
// 本地时间戳 1637887053
```

- toLocalDateTime
- toLocalDate
- toLocalTime

LocalDateTime 转 ZonedDateTime

```java
LocalDateTime ldt = LocalDateTime.now();

ZonedDateTime zdt_local = ldt.atZone(ZoneId.systemDefault());
ZonedDateTime zdt_new_york = ldt.atZone(ZoneId.of("America/New_York"));
```

## 时间调整

### with

支持 LocalDateTime LocalDate LocalTime ZoneDateTime

- withYear 设置指定年
- withMonth 设置指定月
- ...

### 运算

在当前日期时间基础上, 添加指定的时间, 比如: `plusYears(2)` 表示增加 2 年

`plus` 表示增加, `minus` 表示减少

- `plusYears()`  `minusYears()` 年
- `plusMonths()`  `minusMonths()` 月
- `plusDays()`  `minusDays()` 日
- `plusWeeks()`  `minusWeeks()` 周
- `plusHours()`  `minusHours()` 时
- `plusMinutes()`  `minusMinutes()` 分
- `plusSeconds()`  `minusSeconds()` 秒
- `plusNanos()`  `minusNanos()` 纳秒

### 时间片


###  时间片

Period Duration 继承自, TemporalAmount 表示一段日期时间; Period 表示一段日期,  Duration 表示一段时间

- `Duration.ofDays(1)`
- `Duration.ofHours(1)`
- ...

- `Period.ofYears(1)`
- ...


### 运算


`plus(TemporalAmount)` 增加 时间片
`minus(TemporalAmount)` 减少 时间片

```java
LocalDateTime ldt = LocalDateTime.of(2021, 1, 12, 13, 24, 12);
Duration five_minute = Duration.ofMinutes(10);
LocalDateTime ldt_new = ldt.plus(five_minute);
System.out.println(ldt_new);

// 2021-01-12T13:34:12
```

## 日期时间字符串

### 时间日期格式

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();
String str_t = zonedDateTime.format(DateTimeFormatter.ofPattern("MM-dd-yyyy"));
```

```
Symbol  Meaning                     Presentation      Examples
------  -------                     ------------      -------
G       era                         text              AD; Anno Domini; A
y       year-of-era                 year              2004; 04
D       day-of-year                 number            189
M/L     month-of-year               number/text       7; 07; Jul; July; J
d       day-of-month                number            10
g       modified-julian-day         number            2451334
Q/q     quarter-of-year             number/text       3; 03; Q3; 3rd quarter
Y       week-based-year             year              1996; 96
w       week-of-week-based-year     number            27
W       week-of-month               number            4
E       day-of-week                 text              Tue; Tuesday; T
e/c     localized day-of-week       number/text       2; 02; Tue; Tuesday; T
F       day-of-week-in-month        number            3
a       am-pm-of-day                text              PM
h       clock-hour-of-am-pm (1-12)  number            12
K       hour-of-am-pm (0-11)        number            0
k       clock-hour-of-day (1-24)    number            24
H       hour-of-day (0-23)          number            0
m       minute-of-hour              number            30
s       second-of-minute            number            55
S       fraction-of-second          fraction          978
A       milli-of-day                number            1234
n       nano-of-second              number            987654321
N       nano-of-day                 number            1234000000
V       time-zone ID                zone-id           America/Los_Angeles; Z; -08:30
v       generic time-zone name      zone-name         Pacific Time; PT
z       time-zone name              zone-name         Pacific Standard Time; PST
O       localized zone-offset       offset-O          GMT+8; GMT+08:00; UTC-08:00
X       zone-offset 'Z' for zero    offset-X          Z; -08; -0830; -08:30; -083015; -08:30:15
x       zone-offset                 offset-x          +0000; -08; -0830; -08:30; -083015; -08:30:15
Z       zone-offset                 offset-Z          +0000; -0800; -08:00
p       pad next                    pad modifier      1
```




## 知识扩展

### MySql

LocalDate 对应数据库的 Date
LocalTime 对应数据库的 time
LocalDateTime 对应数据库的 datetime

### 时间戳

获取当前时间辍， 单位(毫秒)

```java
Long timestamps = System.currentTimeMillis();
```

MySQL 使用无符号整形存储单位为秒的时间辍


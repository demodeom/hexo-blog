---
title: Java 日期时间
date: 2021-11-12 02:44:09
tags:
	- Java 日期时间
categories:
	- Java 基础
---


## LocalDate LocalTime LocalDateTime

### LocalDate

获取 LocalDate

```java
LocalDate ld = LocalDate.now();
int year = ld.getYear();
int month = ld.getMonth().getValue();
int day = ld.getDayOfMonth();

System.out.println("LocalDate: " + ld);
System.out.println("year: " + year);
System.out.println("month: " + month);
System.out.println("day: " + day);

// LocalDate: 2021-11-11
// year: 2021
// month: 11
// day: 11
```

构建 LocalDate

```java
LocalDate ld = LocalDate.parse("2020-12-09");
LocalDate ld_2 = LocalDate.parse("12-09-2020", DateTimeFormatter.ofPattern("MM-dd-yyyy"));
LocalDate ld_3 = LocalDate.of(2020, 12 , 9);
```

### LocalTime

获取本地时间

```java
LocalTime lt = LocalTime.now();

int hour = lt.getHour();
int second = lt.getSecond();
int minute = lt.getMinute();
int nano = lt.getNano();

System.out.println("LocalTime: " + lt);
System.out.println("hour: " + hour);
System.out.println("second: " + second);
System.out.println("minute: " + minute);
System.out.println("nano: " + nano);

// LocalTime: 10:42:21.036234400
// hour: 10
// second: 21
// minute: 42
// nano: 36234400
```

构建本地时间


```java
LocalTime lt = LocalTime.parse("13:24:30");
LocalTime lt_1 = LocalTime.of(13, 24, 30, 0);
LocalTime lt_2 = LocalTime.parse("13:24:30", DateTimeFormatter.ofPattern("H:i:s"));
```

### LocalDateTime

```java
LocalDateTime ldt = LocalDateTime.now();

int year = ldt.getYear();
int month = ldt.getMonth().getValue();
int day = ldt.getDayOfMonth();
int hour = ldt.getHour();
int second = ldt.getSecond();
int minute = ldt.getMinute();
int nano = ldt.getNano();

System.out.println("LocalDate: " + ldt);
System.out.println("year: " + year);
System.out.println("month: " + month);
System.out.println("day: " + day);
System.out.println("hour: " + hour);
System.out.println("second: " + second);
System.out.println("minute: " + minute);
System.out.println("nano: " + nano);

// LocalDate: 2021-11-11T10:55:10.164656500
// year: 2021
// month: 11
// day: 11
// hour: 10
// second: 10
// minute: 55
// nano: 164656500
```

构建本地日期时间

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


### 时间日期的调整

使用 `with` 方法, 设定指定值; 比如: `withYear(2021)` 设置年份为 2021 年

```java
LocalDate ld = LocalDate.of(2021, 1, 12);

System.out.println("withYear 2020 : " + ld.withYear(2020));
System.out.println("withMonth 2 : " + ld.withMonth(2));
System.out.println("withDayOfMonth 15 : " + ld.withDayOfMonth(15));

// withYear 2020 : 2020-01-12
// withMonth 2 : 2021-02-12
// withDayOfMonth 15 : 2021-01-15
```

- `withYear()`
- `withMonth()`
- `withDayOfMonth()`
- `withHour()`
- `withMinute()`
- `withSecond()`
- `withNano()`

### 日期时间运算

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

```java
LocalDateTime ldt = LocalDateTime.of(2021, 12, 1,13,32, 12);
LocalDateTime ldt_week_one =  ldt.plusWeeks(1);
System.out.println(ldt_week_one);

// 2021-12-08T13:32:12
```

## ZonedDateTime

LocalDateTime 无法与 Long 进行转换
LocalDateTime 没有时区, 无法确定某一时刻
ZonedDateTime 有时区, 可以与 long 进行转换

ZonedDateTime 带时区的日期和时间
ZonedId 新的时区对象
Instant 时刻对象


```java
// 获取本地时区的当前时间
ZonedDateTime zdt_local = ZonedDateTime.now();
System.out.println("local: " + zdt_local);
// local: 2021-11-12T01:28:53.054436+08:00[Asia/Shanghai]

// 获取纽约的当前时间
ZonedDateTime zdt_ny = ZonedDateTime.now(ZoneId.of("America/New_York"));
System.out.println("local: " + zdt_ny);
// New_York: 2021-11-11T12:30:27.255439600-05:00[America/New_York]
```

## LocalDateTime 转 ZoneDateTime

`atZone()` 将 LocalDateTime 转换为 ZoneDateTime; `atZone()` 只是将 LocalDateTime 关联到指定的时区, 并不会进行时间转换

```java
LocalDateTime ldt = LocalDateTime.now();

ZonedDateTime zdt_local = ldt.atZone(ZoneId.systemDefault());
ZonedDateTime zdt_new_york = ldt.atZone(ZoneId.of("America/New_York"));

System.out.println("zdt local: " + zdt_local);
System.out.println("zdt New York: " + zdt_new_york);

// zdt local: 2021-11-12T01:34:59.997936100+08:00[Asia/Shanghai]
// zdt New York: 2021-11-12T01:34:59.997936100-05:00[America/New_York]
```

`withZoneSameInstant()` 将时间从一个时区转换到另一个时区, 比如: 当前上海时间为 `2021-11-12 01:38:26`, 当前

```java
LocalDateTime ldt = LocalDateTime.now();

ZonedDateTime zdt_local = ldt.atZone(ZoneId.systemDefault());
ZonedDateTime zdt_new_york = zdt_local.withZoneSameInstant(ZoneId.of("America/New_York"));

System.out.println("zdt local: " + zdt_local);
System.out.println("zdt New York: " + zdt_new_york);

// zdt local: 2021-11-12T01:38:26.249935800+08:00[Asia/Shanghai]
// zdt New York: 2021-11-11T12:38:26.249935800-05:00[America/New_York]
```

## ZoneDateTime 转 long

```java
long zdt = ZonedDateTime.now().toEpochSecond();
System.out.println(zdt);
// 1636654031
```

## TemporalAmount

Period Duration 继承自, TemporalAmount 表示一段日期时间; Period 表示一段时间,  Duration 表示一段日期

`plus(TemporalAmount)` 增加一段时间
`minus(TemporalAmount)` 减少一段时间

```java
LocalDateTime ldt = LocalDateTime.of(2021, 1, 12, 13, 24, 12);
Duration five_minute = Duration.ofMinutes(10);
LocalDateTime ldt_new = ldt.plus(five_minute);
System.out.println(ldt_new);

// 2021-01-12T13:34:12
```

## 总结

### MySql

LocalDate 对应数据库的 Date
LocalTime 对应数据库的 time
LocalDateTime 对应数据库的 datetime

### 时间戳






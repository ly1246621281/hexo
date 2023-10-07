---
categories:
  - JAVA总结
  - JAVA (--2020)
---
# JAVA8 LocalDate

1.获取当前时间

```javacsharp
 LocalDate today = LocalDate.now();
 System.out.println("今天：" + today);
```

2.获取指定日期的时间

```csharp
 LocalDate appointDate = LocalDate.of(2019, 11, 1);
 System.out.println("2019年11月1日：" + appointDate);
 appointDate = LocalDate.of(2019, Month.NOVEMBER, 2);
 System.out.println("2019年11月2日：" + appointDate);
```

3.获取字符串的时间

```csharp
 // 严格按照ISO yyyy-MM-dd验证
 LocalDate stringDate = LocalDate.parse("2019-11-03");
 System.out.println("2019年11月3日：" + stringDate);
 // 也可以允许自己定义格式
 stringDate = LocalDate.parse("20191104", DateTimeFormatter.ofPattern("yyyyMMdd"));
 System.out.println("2019年11月4日：" + stringDate);
```

4.根据今天日期获取某一天

```csharp
 LocalDate today = LocalDate.now();
 // 本月第一天
 LocalDate dateOfThisMonth = today.with(TemporalAdjusters.firstDayOfMonth());
 System.out.println("本月第一天：" + dateOfThisMonth);
 // 本月第十天
 dateOfThisMonth = today.withDayOfMonth(10);
 System.out.println("本月第十天：" + dateOfThisMonth);
 // 本月最后一天
 dateOfThisMonth = today.with(TemporalAdjusters.lastDayOfMonth());
 System.out.println("本月最后一天：" + dateOfThisMonth);
 // 下个月的3号（最后一天往后推三天）
 dateOfThisMonth = dateOfThisMonth.plusDays(3);
 System.out.println("下个月3号：" + dateOfThisMonth);
 // 本月第三个周三（先获取第一个周三，然后往后推14天）
 dateOfThisMonth = today.with(TemporalAdjusters.firstInMonth(DayOfWeek.WEDNESDAY)).plusDays(7 * 2);
 System.out.println("本月第三个周三：" + dateOfThisMonth);
 // 下个月的第四个周四（先获取下个月的第1天，再获取第一个周四，然后往后推21天）
 dateOfThisMonth = today.with(TemporalAdjusters.firstDayOfNextMonth()).with(TemporalAdjusters.firstInMonth(DayOfWeek.THURSDAY)).plusDays(7*3);
 System.out.println("下个月的第四个周四：" + dateOfThisMonth);
```

5.数据类型转换

```csharp
 // 转字符串
 String todayString = LocalDate.now().toString();
 System.out.println("String-1：" + todayString);
 todayString = LocalDate.now().format(DateTimeFormatter.ofPattern("yyyy/MM/dd"));
 System.out.println("String-2：" + todayString);

 // Local -> Date （这里使用的时间是0点）
 Date todayOfDate = Date.from(LocalDate.now().atStartOfDay(ZoneId.systemDefault()).toInstant());
 System.out.println("todayOfDate：" + todayOfDate);

 // Date -> Local
 LocalDate todayOfLocalDate = new Date().toInstant().atZone(ZoneId.systemDefault()).toLocalDate();
 System.out.println("todayOfLocalDate：" + todayOfLocalDate);
```

6. 获取一段周期内的日期列表

```
/**
 * 获取指定范围内所有日期，包含开始日期和结束日期
 *
 * @return List<LocalDate>
 */
public static List<String> getLocalDateByDay(LocalDate startDate, LocalDate endDate) {
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
    List<String> localDateList = Stream.iterate(startDate, date -> date.plusDays(1)).
            limit(ChronoUnit.DAYS.between(startDate, endDate)).map(date -> date.format(dateTimeFormatter))
            .collect(Collectors.toList());
    localDateList.add(endDate.format(dateTimeFormatter));
    return localDateList;
}
```

7. 当前日期的下一个周日，若当前日期为周日则为今天。

``` LocalDate weekEndRange = startTime.with(TemporalAdjusters.nextOrSame(DayOfWeek.SUNDAY));```



## TODO:

> * TemporalAdjusters.class
> * ChronoUnit.class
---
title: Comparator学习
date: 20201207
tag: JAVA
categories:
  - JAVA总结
  - JAVA（2020--2023）
---

# Comparator学习

​	1. 排序

```java
 List<Integer> list = Lists.newArrayList(3, 5, 8, 6, 1, 10);
 List<Integer> collect1 = list.stream().sorted(Comparator.nullsFirst((a, b) -> a - b > 0 ? 1 : 0)).
                collect(Collectors.toList());
```

排序 整体按照

(a,b) -> a>b  1  a<b -1  a=b  0

a>b  1 降序排，-1升序排.必须 1和-1同时存在，不然比较器不知反正怎么排序。上述案例未排序.

2. 以5为中线，左右两半各进行相应排序

```java
 List collect = list.stream().sorted(Comparator.<Integer>comparingInt(p -> p >= 5 ? -1 : 1).
                thenComparing((a, b) -> a == b ? 0 : (a - b > 0 ? 1 : -1)))
                .collect(Collectors.toList());
  System.out.println(collect);
```

> ```
> sorted(Comparator.<Integer>comparingInt(p -> p == 5 ? 0 : -1)
> 5在後面，
> sorted(Comparator.<Integer>comparingInt(p -> p == 5 ? 0 : 1)
> 5在前面
> ```

3.comparing 方法一

```java
 sort 对象接收一个 Comparator 函数式接口，可以传入一个lambda表达式
         */
        employees.sort((o1, o2) -> o1.getName().compareTo(o2.getName()));

        Collections.sort(employees, (o1, o2) -> o1.getName().compareTo(o2.getName()));

        employees.forEach(System.out::println);


        /**
         * Comparator.comparing 方法的使用
         *
         * comparing 方法接收一个 Function 函数式接口 ，通过一个 lambda 表达式传入
         *
         */
        employees.sort(Comparator.comparing(e -> e.getName()));

        /**
         * 该方法引用 Employee::getName 可以代替 lambda表达式
         */
        employees.sort(Comparator.comparing(Employee::getName));

```

4.comparing 方法二

```
    public static <T, U> Comparator<T> comparing(
            Function<? super T, ? extends U> keyExtractor,
            Comparator<? super U> keyComparator)
    {
        Objects.requireNonNull(keyExtractor);
        Objects.requireNonNull(keyComparator);
        return (Comparator<T> & Serializable)
            (c1, c2) -> keyComparator.compare(keyExtractor.apply(c1),
                                              keyExtractor.apply(c2));
    }
```

和comparing 方法一不同的是 该方法多了一个参数 keyComparator ，keyComparator 是创建一个自定义的比较器。

```
Collections.sort(employees, Comparator.comparing(
                Employee::getName, (s1, s2) -> {
                    return s2.compareTo(s1);
                }));
```

# 使用 ***Comparator.reversed\*** 进行排序

返回相反的排序规则，

```
/**
 *  相反的排序规则
 */
Collections.sort(employees, Comparator.comparing(Employee::getName).reversed());

employees.forEach(System.out::println);
```

输出，

```
Employee{name='Keith', age=35, salary=4000.0, mobile=3924401}
Employee{name='John', age=25, salary=3000.0, mobile=9922001}
Employee{name='Ace', age=22, salary=2000.0, mobile=5924001}
```

 

# 使用 ***Comparator.\***nullsFirst进行排序

当集合中存在null元素时，可以使用针对null友好的比较器，null元素排在集合的最前面

```
employees.add(null);  //插入一个null元素
Collections.sort(employees, Comparator.nullsFirst(Comparator.comparing(Employee::getName)));
employees.forEach(System.out::println);


Collections.sort(employees, Comparator.nullsLast(Comparator.comparing(Employee::getName)));
employees.forEach(System.out::println);
```

 

# 使用 **Comparator\*.\*thenComparing排序**

首先使用 name 排序，紧接着在使用ege 排序，来看下使用效果

```
Collections.sort(employees, Comparator.comparing(Employee::getAge).thenComparing(Employee::getName));
employees.forEach(System.out::println);
```
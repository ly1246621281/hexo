---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# JAVA 请求接收Date类型OR 返回Date类型

[@DateTimeFormat 和 @JsonFormat 注解](https://blog.csdn.net/zhou520yue520/article/details/81348926)



==@DateFormat无效==

一般都是使用@DateTimeFormat把**传给后台的时间字符串转成Date**，使用@JsonFormat**把后台传出的Date转成时间字符串**，但是@DateTimeFormat只会在类似@RequestParam的请求参数（url拼接的参数才生效，如果是放到RequestBody中的form-data也是无效的）上生效，如果@DateTimeFormat放到@RequestBody下是无效的。

　　在@RequestBody中则可以使用@JsonFormat把传给后台的时间字符串转成Date，也就是说**@JsonFormat其实既可以把传给后台的时间字符串转成Date也可以把后台传出的Date转成时间字符串**。

```
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
private Date time;
```
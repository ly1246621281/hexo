---
title: Spring 参数校验
date: 2020.12.29
tag: 'JAVA,Spring'
categories:
  - JAVA总结
  - JAVA（2020--2023）
---

# Spring 参数校验

## 1.Spring参数校验

> Spring MVC支持Bean Validation，通过这个验证技术，可以通过注解方式，很方便的对输入参数进行验证，之前使用的校验方式，都是基于Bean对象的，但是在@RequestParam中，没有Bean对象，这样使得校验无法进行，可以通过使用@Validated注解，使得校验可以进行。

### 1.1 bean参数

#### 一、声明对象

```java
public class User {
    private String id;  
 
    @NotBlank(message = "密码不能为空")
    private String password;
}
```

#### 二、通过@Valid注解使用对象

```java
@RestController
@RequestMapping("/user")
public class UserController {
    @PostMapping
    public User create (@Valid @RequestBody User user) {
        System.out.println(user.getId());
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        user.setId("1");
        return user;
    }
}    
```

@NotBlank 注解所指的 password 字段，表示验证密码不能为空，如果为空的话，上面 Controller 中的 create 方法会将message 中的"密码不能为空"返回。

**例子二：**

①： 首先需要在实体类的相应字段上添加用于充当校验条件的注解，如：@Min,如下代码（age属于User类中的属性）：

```
 @Min(value = 18,message = "年龄不合法")
    private Integer age;
```

② ： 其次在controller层的方法的要校验的参数上添加@Valid注解，并且需要传入BindingResult对象，用于获取校验失败情况下的反馈信息，如下代码：

```
 @PostMapping("/users")
    public User addUser(@RequestBody @Valid User user, BindingResult bindingResult) {
        if(bindingResult.hasErrors()){
            System.out.println(bindingResult.getFieldError().getDefaultMessage());
            return null;
        }
        return userResposity.save(user);
    }
```

bindingResult.getFieldError.getDefaultMessage()用于获取相应字段上添加的message中的内容，如：@Min注解中message属性的内容

[@Valid学习][https://blog.csdn.net/weixin_41133233/article/details/90448300]

### 1.2  @RequestParam

#### 声明@Validated并加上校验注解 (未验证成功...！)

```java
package com.github.yongzhizhan.draftbox.controller;

import com.github.yongzhizhan.draftbox.model.Foo;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpStatus;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;
import javax.validation.constraints.Size;

@RestController
@SuppressWarnings("UnusedDeclaration")
@Validated
public class IndexController {
    @ResponseBody
    @RequestMapping(value = "validString", method = RequestMethod.GET)
    @ResponseStatus(HttpStatus.OK)
    public String validString(
            @RequestParam(value = "str", defaultValue = "")
            @Size(min = 1, max = 3)
            String vStr){

        return vStr;
    }
}
```

## 2.申明异常处理

### 2.1 局部处理类

controller类内使用@ExceptionHandler 去处理异常。

```java
 @ExceptionHandler({Exception.class})
    public ResultVO testArithmeticException(Exception e){
        log.error("Exception:[{}]", e);
        return ResultVO.unsuccess().setMessage("拉回数据库处理仓数异常");
    }
```



### 2.2 全局处理类

```java

package com.zj.controller;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
//处理异常
@ControllerAdvice
public class HandleForException {    
    @ExceptionHandler({ArithmeticException.class})    
    public String testArithmeticException(Exception e){       
        System.out.println("ArithmeticException:"+e);       
        return "error";    
    }
}

```

参考：

[validation与 springboot 结合](https://www.cnblogs.com/yuxinkuan/p/12054964.html)
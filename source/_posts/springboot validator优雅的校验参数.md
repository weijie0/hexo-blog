---
layout:     post
title:      "springboot validator优雅的校验参数"
subtitle:   "springboot validator"
date:       2020-03-24 12:00:00
author:     "weijie"
catalog:    true
tags:
    - springboot
    - validator
---



## 前言

web开发过程中，绕不开要做参数验证，例如简单的必填验证，正则验证，以及一些复杂的组合验证。如果参数少实现起来还比较容易，但是如果参数较多的话，又没有使用一些验证框架，就会出现大量的if else代码，并且会出现大量的重复代码，那如何优雅的校验参数呢，让我们看看spring是怎么实现的。

## 什么是Validator

Bean Validation是Java定义的一套基于注解的数据校验规范，目前已经从JSR 303的1.0版本升级到JSR 349的1.1版本，再到JSR 380的2.0版本（2.0完成于2017.08），已经经历了三个版本 。

> 如果已经引用了spring-boot-starter-web,就不要需要引用spring-boot-starter-validation了,本例就不再引用

```
<dependency>
  <groupId>org.hibernate.validator</groupId>
  <artifactId>hibernate-validator</artifactId>
</dependency>
<dependency>
  <groupId>javax.validation</groupId>
  <artifactId>validation-api</artifactId>
</dependency>
```

> 但是我们在集成的时候发现没有对应的包，springboot版本在2.3，这就需要我们收到添加aop的支持

```
<!-- 引入validation支持 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 注解介绍

### validator内置注解

| **注解**                      | **详细信息**                                             |
| ----------------------------- | -------------------------------------------------------- |
| `@Null`                       | 被注释的元素必须为 `null`                                |
| `@NotNull`                    | 被注释的元素必须不为 `null`                              |
| `@AssertTrue`                 | 被注释的元素必须为 `true`                                |
| `@AssertFalse`                | 被注释的元素必须为 `false`                               |
| `@Min(value)`                 | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| `@Max(value)`                 | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| `@DecimalMin(value)`          | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| `@DecimalMax(value)`          | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| `@Size(max, min)`             | 被注释的元素的大小必须在指定的范围内                     |
| `@Digits (integer, fraction)` | 被注释的元素必须是一个数字，其值必须在可接受的范围内     |
| `@Past`                       | 被注释的元素必须是一个过去的日期                         |
| `@Future`                     | 被注释的元素必须是一个将来的日期                         |
| `@Pattern(value)`             | 被注释的元素必须符合指定的正则表达式                     |

### Hibernate Validator 附加的 constraint

| **注解**    | **详细信息**                           |
| ----------- | -------------------------------------- |
| `@Email`    | 被注释的元素必须是电子邮箱地址         |
| `@Length`   | 被注释的字符串的大小必须在指定的范围内 |
| `@NotEmpty` | 被注释的字符串的必须非空               |
| `@Range`    | 被注释的元素必须在合适的范围内         |
| `@NotBlank` | 验证字符串非null，且长度必须大于0      |

**注意**：

- @NotNull 适用于任何类型被注解的元素必须不能与NULL

- @NotEmpty 适用于String Map或者数组不能为Null且长度必须大于0

- @NotBlank 只能用于String上面 不能为null,调用trim()后，长度必须大于0

## 如何使用

对于简单的参数检验，可以直接在方法或类上加上@Valid并配合validator注解使用，复杂对象的检验则注解在对象属性上加validator注解即可，需要注意的是对象嵌套可能会导致注解失效，需要要嵌套的对象上加上@Valid注解

## 自定义异常处理

创建一个`GlobalExceptionHandler`类，在类上方添加`@RestControllerAdvice`注解然后添加以下代码：

```
/**
* 单个参数验证
*/
@ExceptionHandler(ValidationException.class)
public Response handleMethodArgumentNotValidException(ValidationException e) {
    log.error(e.getMessage(), e);
    return new Response(ExceptionMsg.ParamError.getCode(), e.getMessage());
}
/**
* 对象验证
*/
@ExceptionHandler(MethodArgumentNotValidException.class)
public Response handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
    log.error(e.getMessage(), e);
    return new Response(ExceptionMsg.ParamError.getCode(),e.getBindingResult().getFieldError().getDefaultMessage());
}
```

## 参考

[SpringBoot入门二十二,使用Validation进行参数校验](https://blog.51cto.com/1197822/2467086)
[SpringBoot如何优雅的校验参数](https://lqcoder.com/p/4cd8a59d.html)


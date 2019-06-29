---
title: "SpringBoot整合Thymeleaf"
date: 2018-05-19T14:04:04+08:00
lastmod: 2018-05-19T14:04:04+08:00
keywords: []
description: ""
tags: ["SpringBoot"]
categories: ["SpringBoot"]
author: ""
---

* 引入依赖

```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

#### 无数据源

* application.properties配置文件

```
#设定thymeleaf文件路径 默认为src/main/resources/templates
spring.freemarker.template-loader-path=classpath:/templates/*.html
#thymeleaf start
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
#开发时关闭缓存,不然没法看到实时页面
spring.thymeleaf.cache=false

```

* hello.html

```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" >
<head>
    <title>Form Submission</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />

</head>
<body>
<!--/*@thymesVar id="name" type="java.lang.String"*/-->
<p th:text="'Hello！, ' + ${name} + '!'" >hello</p>

</body>
</html>
```

* HelloController.java

> @Controller返回一个页面而不是一个值，如果是@RestController无法返回thymeleaf等页面，返回的只是return的内容。 

```
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
public class HelloController {

    @RequestMapping("/hello")
   public String greet(Model model){
        model.addAttribute("name","world");
        return "hello";
   }

}

```

#### 配置数据源

* 文件结构

![结构](https://raw.githubusercontent.com/pugongyingbo/pugongyingbo.github.io/master/images/struct.png)


* application.properties配置文件

```
##端口号
server.port=8080
##检查 mybatis 配置是否存在，一般命名为 mybatis-config.xml
mybatis.check-config-location =true
##配置文件位置
mybatis.config-location=classpath:mybatis/mybatis-config.xml
## mapper xml 文件地址
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
mybatis.typeAliasesPackage=com.example.demo.entity
##数据库url
spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8
##数据库用户名
spring.datasource.username=root 
##数据库密码
spring.datasource.password=root
##数据库驱动
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
#设定thymeleaf文件路径 默认为src/main/resources/templates
spring.freemarker.template-loader-path=classpath:/templates/*.html
#thymeleaf start
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
#开发时关闭缓存,不然没法看到实时页面
spring.thymeleaf.cache=false
```

* User

```
package com.example.entity;

import lombok.Data;
import org.apache.ibatis.type.Alias;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotEmpty;

@Data
@Alias("user")
public class User {

    private int id;
    @NotEmpty
    private String name;

    @NotEmpty
    @Email
    private String account;
    @NotEmpty
    private String password;
    @NotEmpty
    private String birthday;

}

```

* UserMapper

```
package com.example.dao;

import com.example.entity.User;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface UserMapper {

    List<User> getUserList();

}

```

* userMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.example.dao.UserMapper">
    <resultMap id="user" type="com.example.entity.User"/>
    <parameterMap id="user" type="com.example.entity.User"/>

    <select id="getUserList" resultMap="user">
            select  * from user
    </select>
</mapper>
```

* mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD SQL Map Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
        <typeAliases>
            <typeAlias alias="Integer" type="java.lang.Integer" />
            <typeAlias alias="Long" type="java.lang.Long" />
            <typeAlias alias="HashMap" type="java.util.HashMap" />
            <typeAlias alias="LinkedHashMap" type="java.util.LinkedHashMap" />
            <typeAlias alias="ArrayList" type="java.util.ArrayList" />
            <typeAlias alias="LinkedList" type="java.util.LinkedList" />
            <typeAlias alias="user" type="com.example.entity.User"/>
        </typeAliases>
</configuration>
```

* UserController 

```
package com.example.controller;

import com.example.dao.UserMapper;
import com.example.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class UserController {

    @Autowired
    private UserMapper userMapper;

    @RequestMapping("/userList")
    public String greetingForm(ModelMap model) {

        model.addAttribute("userList", userMapper.getUserList());
        return "userList";
    }

}

```

* userList.html
 
 > xmlns:th="http://www.thymeleaf.org"命名空间，将页面转化为动态的视图，需要进行动态处理的元素使用“th:”前缀；两个link引入bootstrap框架，通过@{}引入web静态资源（括号里面是资源路径）访问model中的数据通过${}访问

```
<!DOCTYPE HTML>
<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Spring Boot Thymeleaf</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<table class="table table-hover table-condensed">
    <legend>
        <strong>用户列表</strong>
    </legend>
    <thead>
    <tr>
        <th>编号</th>
        <th>姓名</th>
        <th>账号</th>

    </tr>
    </thead>
    <tbody>
    <tr th:each="user : ${userList}">
        <th scope="row" th:text="${user.id}"></th>
        <td th:text="${user.name}"></td>
        <td th:text="${user.account}"></td>

    </tr>
    </tbody>
</table>

</body>
</html>
```

 * 显示效果
 
![userlist](https://raw.githubusercontent.com/pugongyingbo/pugongyingbo.github.io/master/images/userlist.png)
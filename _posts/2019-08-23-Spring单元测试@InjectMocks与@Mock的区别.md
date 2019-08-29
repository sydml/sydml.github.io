---
layout:     post
title:      Spring单元测试@InjectMocks与@Mock的区别
subtitle:   单元测试
date:       2019-08-23
author:     SYDML
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Spring
---

## @InjectMocks与@Mock的区别
* @InjectMocks注入的实例会直接调用该实例的方法
* @Mock注入方式则不会调用注入实例的方法，而是被mock掉了
* 在@Before方法里用MockitoAnnotations.initMocks(this)时，会将@Mock标记的示例注入到@InjectMocks标记的实例中作为mock对象使用

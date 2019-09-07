---
layout: post
title: "Spring动态添加Bean到容器中"
description: Spring动态添加Bean到容器中
modified: 2019-09-06
category: Java
tags: [Java]
---

# 一、方法

    // 或者直接获取Autowired注入的applicationContext
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	// 将applicationContext转换为ConfigurableApplicationContext
	ConfigurableApplicationContext configurableApplicationContext = (ConfigurableApplicationContext) applicationContext;
	// 获取bean工厂并转换为DefaultListableBeanFactory
	DefaultListableBeanFactory defaultListableBeanFactory = (DefaultListableBeanFactory) configurableApplicationContext.getBeanFactory();
	// 通过BeanDefinitionBuilder创建bean定义
	BeanDefinitionBuilder beanDefinitionBuilder = BeanDefinitionBuilder.genericBeanDefinition(ONE_CLASS.class);
	// 设置属性值或者引用
	beanDefinitionBuilder.addPropertyValue("propertyKey", "propertyValue");
	beanDefinitionBuilder.addPropertyReference("propertyKey", "anotherBeanName");
	// 注册bean
	defaultListableBeanFactory.registerBeanDefinition("beanId",beanDefinitionBuilder.getBeanDefinition());

# 二、参考

1.[Spring动态增加新的Bean到容器中](https://www.v2ex.com/t/307327)
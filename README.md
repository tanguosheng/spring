MyBatis Spring Adapter
======================

spring 集成 Mybatis 桥接包 中文注释参考项目

从这里开始看：

org.mybatis.spring.SqlSessionFactoryBean.afterPropertiesSet 
（SqlSessionFactoryBean 构建 DefaultSqlSessionFactory 就有了 里边的 Configuration 对象）

构建 Environment 时会用到 SpringManagedTransactionFactory，这个对象是事务管理工厂，
有了它就能从spring上下文中获取当前线程应该使用哪个 connection 来管理事务

->
如果配置了事务 就有了 DataSourceTransactionManager (来自 spring), 需要把 构建好的 DataSource 传进去
 DataSource 就由spring管理了

->
spring 如何为 mapper 接口生成动态代理：

@MapperScan 注解 @Import 了 MapperScannerRegistrar 类，这个类继承自 ImportBeanDefinitionRegistrar，
那么在 spring 容器启动时候会调用到 registerBeanDefinitions 方法，
registerBeanDefinitions 方法 中向spring容器中注册了 MapperScannerConfigurer Bean，
这个 MapperScannerConfigurer 继承自 BeanDefinitionRegistryPostProcessor，
so ，容器启动会执行到这个bean 的 postProcessBeanDefinitionRegistry 方法，
其中利用 ClassPathMapperScanner 的 scan 方法扫描配置的包名，
spring 中默认扫描包不能为接口生成Bean定义，这里重写了isCandidateComponent方法，只扫描到mapper接口，
众所周知，spring不能为接口生成实例，
这里做了一个技巧，将扫描到的接口的 BeanClass 设置成 MapperFactoryBean.class (它是一个FactoryBean)，
在 MapperFactoryBean 中 getObject 方法 调用 MyBatis 的 SqlSession.getMapper() 为 mapper 接口生成动态代理。



[![Build Status](https://travis-ci.org/mybatis/spring.svg?branch=master)](https://travis-ci.org/mybatis/spring)
[![Coverage Status](https://coveralls.io/repos/mybatis/spring/badge.svg?branch=master&service=github)](https://coveralls.io/github/mybatis/spring?branch=master)
[![Maven central](https://maven-badges.herokuapp.com/maven-central/org.mybatis/mybatis-spring/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.mybatis/mybatis-spring)
[![Sonatype Nexus (Snapshots)](https://img.shields.io/nexus/s/https/oss.sonatype.org/org.mybatis/mybatis-spring.svg)](https://oss.sonatype.org/content/repositories/snapshots/org/mybatis/mybatis-spring/)
[![License](http://img.shields.io/:license-apache-brightgreen.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)

![mybatis-spring](http://mybatis.github.io/images/mybatis-logo.png)

MyBatis-Spring adapter is an easy-to-use Spring bridge for MyBatis sql mapping framework.

Supported Versions
------------------

- 1.3.x - Continued support for Java 6 and 7
- master (2.0.x) - Support for Java 8, Spring 5, and Junit 5 plus other java 8 requirements

Essentials
----------

* [See the docs](http://mybatis.github.io/spring/)

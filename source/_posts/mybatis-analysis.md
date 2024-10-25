---
title: mybatis-analysis
date: 2023-01-29 11:13:55
tags: mybatis analysis
---
## mybatis源码分析

### 一、与Spring集成，自启动过程

在mybatis jar包中，spring.factories中写了自动配置类`MybatisAutoConfiguration`

#### 配置类如何加载到项目中

搭配使用`@ConfigurationProperties`，`@EnableConfigurationProperties`
重点查看类：`MybatisProperties`，`MybatisAutoConfiguration`，在构造函数里注入了配置类的bean

> 延伸：如果配置信息不想以xml配置文件给到，可以`new XxProperties()`，再在该方法上加入`@Bean`,同样也能注入配置类的bean。这种方式对使用第三方jar包并且想改变其配置信息很好用。

### 二、初始化过程

#### 总览

 ![](https://cc.hjfile.cn/cc/img/20230129/20230129095640273135.png)
 ![](https://cc.hjfile.cn/cc/img/20230129/2023012910144502695747.png)

#### Configuration类

 ![](https://cc.hjfile.cn/cc/img/20230129/202301291000109815653.png)
 ![](https://cc.hjfile.cn/cc/img/20230129/2023012910022560728745.png)
`XMLMapperBuilder`：mybatis将mapper相关的配置(xml和接口)读取为一个`XMLMapperBuilder`对象，并通过`parse()`方法进行解析。
`parse()`方法做了主要做了两件事，加载xml文件和加载class文件。

#### 调试点

##### 1、xml加入到Configuration

`SqlSessionFactoryBean#buildSqlSessionFactory`点进去`xmlConfigBuilder.parse();`创建了`xmlConfigBuilder`
`xmlConfigBuilder`里面`XMLMapperBuilder.buildStatementFromContext();`创建了`XMLStatementBuilder`
在`MapperBuilderAssistant#addMappedStatement`，添加了`mappedStatement`，该类存放的是xml对应的数据。
 ![](https://cc.hjfile.cn/cc/img/20230129/2023012910105360620073.png)

 ##### 2、接口类加入到Configuration

 `SqlSessionFactoryBean#buildSqlSessionFactory` -> `mapperRegistry.addMapper(type)` ->`knownMappers.put(type, new MapperProxyFactory<>(type))`,`knownMappers`存放的就是接口类。

### 三、执行过程

#### 疑问：调用mapper接口类怎么就能查到数据-mybatis中mapper代理的生成过程

##### UserMapper.java成为动态代理bean的创建过程

在程序启动方法`main`上面有个注解`@MapperScan`，点进去会发现这里引进了一个bean：`@Import(MapperScannerRegistrar.class)`，重点是这个方法：`registerBeanDefinitions`,引进了一个bean :`MapperScannerConfigurer`
再进去`MapperScannerConfigurer#postProcessBeanDefinitionRegistry`，进入到方法：`scanner.scan()`
最终到`ClassPathMapperScanner#processBeanDefinitions`，`definition.setBeanClass(this.mapperFactoryBeanClass);`
也就是`UserMappe.java`实现类是`MapperFactoryBean`

#### 调用方法`usermapper.selectByUsername("admin")`

最终实现类是代理类`MapperProxy`，调用方法时就是执行`MapperProxy#invoke`方法，重点看类`MapperMethod`

+ `SqlCommand`里面含sql
+ `MethodSignature`是对应的接口类方法

## MybatisPlus源码分析

#### 启动过程

+ mybatis自动配置类：MybatisPlusAutoConfiguration
+ 配置类：MybatisPlusProperties

#### 调试，重点看加入MappedStatement的过程

SqlSession工厂：`SqlSessionFactoryBean`
可以看到`MybatisSqlSessionFactoryBean#afterPropertiesSet`,调用`buildSqlSessionFactory`,方法开始执行Mybatis的初始化启动过程。
`com.baomidou.mybatisplus.MybatisMapperAnnotationBuilder#parse`，添加`MappedStatement`到mybais configuration中
`MybatisConfiguration#addMappedStatement()`,调整后的SQL优先级：xmlSql > sqlProvider > crudSql
`AbstractSqlInjector,inspectInject()`,注入crudsql

+ `com.baomidou.mybatisplus.toolkit.TableInfoHelper#initTableInfo` 构造成Table 信息
+ 遍历注入方法`AbstractMethod.inject`

## 多数据源

#### 启动过程

自启动`DynamicDataSourceAutoConfiguration`，定义了动态数据源:`DynamicRoutingDataSource(extends AbstractRoutingDataSource)`
在`DynamicDataSourceAutoConfiguration`初始化的时候，对多数据源分类放在map里。

#### 方法在使用切换数据源原理

在调用mybatis，调用到方法`getConnection()` -> `AbstractRoutingDataSource.getConnection()` -> `DynamicRoutingDataSource.getDataSource()`来确定用哪个数据源

#### 如何使用

`@DS(DBConstants.DATASOURCE_ZHIGOU)`

## git demo地址

https://github.com/gaoqiuling/mybatisdemo
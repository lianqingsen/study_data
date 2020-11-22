<h2>Spring-持久层整合

**Spring框架为什么要与持久基础进行整合**

  ```markdown
JavaEE开发需要持久层进行数据库的访问操作。
JDBC hibernate myBatis进行持久开发过程存在大量的代码冗余
Spring基于模板设计模式对于上述的持久层技术进行了封装
  ```

Spring可以与那些持久层技术进行整合？

```markdown
JDBC   hibernate（JPA）不 Mybatis 
```

Spring与Mybatis整合

Mybatis开发步骤：

```markdown
实体
实体别名
表
创建DAO接口
实现Mapper文件
注册Mapper文件
MybatisAPI调用
```

2.Mybaits在开发过程中存在的问题

```markdown
配置繁琐    代码冗余
实体
实体别名    配置繁琐 
表
创建DAO接口
实现Mapper文件
注册Mapper文件 配置繁琐
MybatisAPI调用 代码冗余
因为mybatis独立开发的时候会出现的问题，所以Spring在后续整合mybatis全部解决的这个问题，来简化mybatis。
```

Spring与Mybatis整合思路分析

![image-20201120150657645](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201120150657645.png)

Spring与Mybatis整合整合的开发步骤

配置文件(ApplicationContext.xml)进行相关配置

```xml
<bean id="dateSource" class=""></bean>
<!--创建SqlSessionFactory-->
<bean id="ssfb" class="sqlSessionFactoryBean">
    <property name="dateSource" ref=""></property>
    <property name="typeAliasesPackage">
    指定 实体类所在的包  列：com.qing.entity
    </property>
    <property name="mapplerLocations">
    指定 配置文件（映射文件）的路径  还有通用配置
        com.qing.mapper/*Mapper.xml
    </property>
</bean>
<!--DAO接口的实现类
主要的作用：获取Session对象 通过session.getMapper() 获取xxxDao实现类对象
-->
<bean id="scanner" class="MapperScannerConfigure">
<property name="sqlSessionFactoryName" value="ssfb"></property>
    <property name="basePackage">
    指定 DAO接口放置的包  com.qing.dao
    </property>
</bean>
```

Spring与Mybatis整合编码

搭建开发环境jar

```xml
  <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.5</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.3</version>
        </dependency>
```

Spring配置文件的配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--连接池 druid-->
```

**Spring与Mybatis细节**

问题：Spring与Mybatis整合后，为什么DAO不提交事务，但是数据能够插入数据库中？

~~~markdown
Connection ——————> tx
Mybatis(Connectin)
本质上控制连接对象(Connection) --->连接池（DataSource）
1.Mybatis提供的连接池对象 --->创建Conncetion
 Connection.setAutoCommit(false) 手工控制了事务，操作完成后，需要手工提交
2.Druid作为连接池  ---->创建Conncetion
 Connection.setAutoCommit(true) true默认值 保持自动控制事务，一条sql执行完后自动提交
~~~

**Spring的事务处理**

什么是事务？

```markdown
保证业务操作完整行的一种数据库机制

事务的4特点：A C I D
A 原子性 （Atomicity）
原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
C 一致性（Consistency）
事务前后数据的完整性必须保持一致。
I 隔离性（Isolation）
事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离
D 持久性（Durability）
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响
```

2.如何控制事务

```markdown
JDBC
	connection.setAutoCommit(False);
	Conncetion.commit():
	Conncetion.rollback()
Mybatis
	Mybatis自动开启事务
	
	Sqlsession.commit()
	Sqlsession.rollback();
结论：控制事务的底层 都是Conncetion对象完成的
```

**Spring控制事务的开发**

```markdown
Spring是通过AOP方式进行事务开发
```

**第一步：原始对象**

```java
public class XXXUserServiceImpl(){
    1.原始对象 --->原始方法 --->核心功能 --->（业务处理+DAO调用）
    2.DAO作为Service成员变量，通过依赖注入方式进行赋值
}
```

**第二步：额外功能**

```markdown
org.springframework.jdbc.datasource.DataSoureTransactionManage;
注入DateSource
```

**第三步：切入点**

```@
@transactional
事务的额外功能加给那些业务方法

1.类上 类中所有的方式都会加入事务
2.方法上，这个方法会加入事务
```

**第四步：组装切面**

```xml
组装切面的组成:切入点+额外功能
<tx:annotation-driven transcation-manager="">
```

Spring控制事务的编码

搭建开发环境jar

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.1.2.RELEASE</version>
        </dependency>
```

编码

```xml
    <!--第一步 创建原始对象-->
    <bean id="userService" class="com.qing.service.UserSericeImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
    <!--第二步 额外功能DataSoureTransactionManage-->
    <bean id="dataSoureTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--第四步 组装切面-->
    <tx:annotation-driven transaction-manager="dataSoureTransactionManager"/>
</beans>
```

Spring中的事务属性(Transaction Attribute)

什么是事务属性

```markdown
属性：描述物体的一系列值
事务属性：描述事务特征的一系列值
1.隔离事务
2.传播事务
3.只读事务
4.超时事务
5.异常事务
```

2.如何添加事务属性

```markdown
@Transaction(isloation=,propagetion=,readonly=,timeout=,rollbackfor,norollbackfor=,)
```

事务属性详解

**1.隔离事务(ISLATION)**

```markdown
概念：描述了事务决解并发问题的特征
1.什么的是并发
	多个事务（用户）在同一时间，访问操作了相同的数据
	同一时间：0.0000 几面  微小前  微小后
2.并发会产生那些问题
	1.脏读
	2.不可重复读
	3.幻影读
并发问题如何解决
	通过事务属性解决，隔离属性中设置不同的值，决解并发处理过程的问题
```

事务并发产生的问题

1.脏读

```markdown
什么是脏读：
一个事务，读取了另一个没有提交的事务数据，会在本事务中产生数据不一致的问题
解决方案：@Transaction(ISLATION=Isolation.READ.COMMITTED)
```

2.不可重复读

```markdown
一个事务 多次读取相同的数据 但是读取的结果不一样 会在本事务中产生数据不一致的问题
注意：不是脏读  一个事务中
解决方法：@Transaction（(ISLATION=ISOLATION.REPEATABLER.READ)
本质：一把行锁
```

3.幻影读

```markdown
一个事务 多次对整表进行查询统计，但是结果并不一样，会在本事务中产生不一致的问题
解决方案：@Transaction(IsoLation=Isolation.Serializable)
本质：表锁
```

![image-20201121093652617](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121093652617.png)

默认的隔离属性

```markdown
ISOLATION_DEFAULT：会调用不同数据所设置的默认隔离属性

Mysql：repeatable——read
oracle:read_committed
```

查看数据库默认隔离属性

MYSQL

```xml
select @@tx_isolation
高版本：select @@transaction_isolation
```

**2.传播事务**

```markdown
概念：他描述了事务解决嵌套的问题的特征
什么叫做事务的嵌套，他值的是一个大的事务中，包括了若干个小的事务
问题：大事务中融入了很多小的事务，他们批次影响，最终会导致外部大的事务，丧失了事务的原子性。
```




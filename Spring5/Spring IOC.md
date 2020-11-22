Spring IOC三种注入方式（接口注入、Setter注入、构造器注入）

构造注入：

spring的配置文件

```xml
    <bean id="customer" class="com.qing.entity.Customer">
        <constructor-arg value="张三"></constructor-arg>
        <constructor-arg value="123"></constructor-arg>
    </bean>
```

构造方法重载

```markdown
构造参数个数不用时：通过constructor-arg标签的数量进行区分

构造参数个数相同是：通过constructor-arg标签中的type的 进行区分类型
```

注入的总结

未来的实战中，应用spring的set注入还是构造注入？

```markdown
答案：set注入会更多
```

**反转控制与依赖注入（面试重点）**

1.反转控制（IOC Inverse of Control，简称IOC）属于一种思想

```markdown
控制：对于成员变量赋值的控制权
反转控制：把对于成员变量赋值的控制权，从代码中反转（转移）到sping工厂和配置文件中完成。
  好处：解耦合
实现原理：工厂设计模式
```

2.依赖注入（Dependency Injection，简称DI）：实现方式

```markdown
注入：通过Spring的工厂及配置文件，为对象（bean、组件）的成员变量赋值
依赖注入：当一个类需要另一个类时，就意味着依赖，一旦出现依赖，就可以把另一个了类作为成员变量，最终通过spirng配置文件进行注入（赋值）。
好处：解耦合
```

**Spring工厂创建复杂对象**

1.要想了解这个概念，我们首先先知道什么是复杂对象

```markdown
复杂对象：不能直接通过new 构造方法创建对象
例如：conncection
SqlSessionFactory
```

**Spring工厂创建复杂对象的3种方式**

第一种：FactoryBean接口

开发步骤
第一 实现FactoryBean接口

```java
public class ConnectionFactoryBean implements FactoryBean<Connection> {
    //用于书写创建复杂对象的代码,把复杂对象作为方法的返回值 返回
    public Connection getObject() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection con= DriverManager.getConnection("jdbc:mysql://localhost:3306/test","root","root");
        return con;
    }
    //返回 所创建复杂对象的class对象
    public Class<?> getObjectType() {
        return Connection.class;
    }
    //如果为true 只要创建一次
    //如果为false 每一次调用 都需要创建一个新的复杂对象
    public boolean isSingleton() {
        return false;
    }
}
```

第二 Spring配置文件的配置

```markdown
如果Class种指定的类型是FactoryBean接口的实现类，那么通过id值获得的是这个类所创建的复杂对象 Connection
<bean id="conn" class="com.qing.factoryBean.ConnectionFactoryBean"></bean>
```

细节

```markdown
如果就想获取FactoryBean类型对象 context.getBean("&conn")
    获取就是ConncetionFactory对象
```

isSingleton方法0

```markdown
返回true只会创建一个复杂对象
返回false每一次都会创建新的对象
问题：根据这个对象特点，决定是返回true(SqlSessionFactory)还是false（Conncetion）
```

mysql高版本连接创建时，需要制定SSL证书，解决问题的方式

```markdown
url="jdbc:mysql://localhost:3306/test?useSSL=false"
```

依赖注入的体会（DI）

```markdown
把ConectionFatoryBean中依赖的4个字符串信息，进行配置文件注入
好处：解耦合
 <bean id="conn" class="com.qing.factoryBean.ConnectionFactoryBean">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test?useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>
```

FactoryBean的是实现原理(简易版)

```markdown
接口回调
1.为什么Spring规定FactoryBean接口 实现 并且 getObject()?
context.getBean("conn") 获取是复杂对象 Connection 而没有获取 ConnectionFactoryBean（）

Spring 内部运行流程
	1.通过conn获得 ConnectionFatoryBean类的对象，进而通过instanceof来判断出是FactoryBean接口的实现类
	2.Spring按照规定 getObject()--> Connection0
    3.返回Conncetion对象
```

第二种：实例工厂

```xml
避免Spring框架的侵入
整合遗留系统、

开发步骤
    <bean id="connFactory" class="com.qing.factoryBean.ConnectionFactory"></bean>
    <bean id="conn1" factory-bean="connFactory" factory-method="getConnection"></bean>
```

第三种：静态工厂

```xml
开发步骤
   <bean id="staticConn" class="com.qing.factoryBean.StaticConnectionFactory" factory-method="getConnection"></bean>
```

控制Spring工厂创建对象的次数

FactoryBean  isSingleton  只是对象复杂对象控制创建对象，也没有控制对象，如果此时通过Spring工厂创建的是简单对象如何控制创建对象的次数呢 ，这就是我们所要思考的问题。

1.如何控制简单对象的创建次数。

```markdown
 <bean id="account" class="com.qing.scope.Account" scope="prototype"></bean>
 scope="singleton"只会创建一次简单对象
 scope="prototype"每一次都会创建
```

3.为什么要控制对象的创建次数？

```markdown
好处：节省不别要的内存浪费
```

什么样子的对象只创建一次  

```java
SqlSessionFactory
DAO
Service
可以共享数据线程安全的情况下使用
```

什么样子的对象，每次创建都是新的

**对象的生命周期**

1.什么对象的生命周期

```markdown
生命周期指的是一个对象创建、存活、到消亡的一个完整过程
```

2.为什么要学习生命周期

```markdown
由Sping负责对象的创建、存活，销毁、了解生命周期，有利于我们使用Spring为我们创建对象
```

生命周期的3个阶段

1.创建阶段

1.Spring工厂何时创建对象

```markdown
当scope="singleton" Spring工厂创建的同时完成对象的创建，在这种情况下，Spring工厂也需要在获取对象完成创建对象 可以这配置文件中设置
<bean  lazy-init="true"></bean> 懒汉初始化

当scope=“prototype” Spring工厂会在获取对象的同时完成创建对象
context.getBean("")获取对象
懒汉式与饿汉式
```

 初始化阶段

```markdown
Spring工厂在创建对象后，调用对象的初始化方法，完成对应的初始化操作

初始化方法提供：程序员根据需求，提供初始化方法，最终完成初始化操作
初始化方法调用：Spring工厂进行调用
```

第一种方式：实现initializingBean接口

```java
//程序员根据需求，实现初始化方法，完成初始化操作
public void afterPropertiesSet() throws Exception 
```

第二种方式：对象中提供一个普通的方法

```markdown
public void myInit(){  //方法名自定义
}
//然后在配置文件<bean>标签中 增加 init-method='myInit'属性
<bean id="life" class="xxx.life" init-method="myInit"></bean>
```

细节分析

```markdown
1.如果一个对象即实现initializingBean 同时又提供了普通的初始化方法 顺序是如何呢
答案：先执行initializingBean再普通初始化的方法
```

```markdown
2.注入一定发生在初始化操作的前面
3.什么叫做初始化操作
	资源的初始化：数据库 IO 网络（了解即可）
```

销毁阶段

```markdown
Spring工厂销毁对象前，会调用对象的销毁方法，完成销毁操作

1.Spring什么时候销毁所创建的对象
答：context.close();
销毁方法：程序员根据自己的需求，定义销毁方法， 完成销毁操作
	底层调用还是Spring工厂进行调用
```

跟初始化阶段相似，两种方式

第一种方式：DisposableBean接口

```java
 public void destroy() throws Exception {  }
```

第二种方式：自定义一个普通的销毁方法

```java
 public void myDestroy() throws Exception {}
//并且在配置文件<bean>设置 destroy-method="myDestory"
<bean id="life"destroy-method="myDestroy" class="xxx.life" ></bean>
```

细节分析：

销毁方法的操作只适合与scope="singleton"

什么叫做销毁操作

```markdown
主要指的是：资源的释放操作：io.close() connection.close()
```

<h3>配置文件参数化</h3>

```markdown
把Spring配置文件中需要经常修改的字符串信息，转移到一个更小的配置文件中

1.Spring的配置文件中那些存在需要经常修改的字符串？
	存在 以数据库连接相关的参数 代表
2.经常变化的字符串，在Spring的配置文件中，直接修改
	不利于项目维护（修改）
3.转移到一个小的配置文件（.properties）
	利于修改
配置文件参数化：利于Spring配置文件维护（修改）
```

Maven细节：
当Maven开发的时候分Java与resources这两个目录，一旦编译之后，就把这两个目录合二为一，所以resource包在classPath目录下：

![image-20201113095836690](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201113095836690.png)

![image-20201113112155272](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201113112155272.png)

在Spring配置文件中通过${key}获取小配置文件中对应的值

<h3>自定义类型转换器</h3>

```markdown
原因：当Spring内部没有提供特定类型转换器时，而程序员在应用的过程中还需要用到， 那么就需要程序员自己定义类型转换了
```

开发步骤：

```java
第一步：类实现 implement Converter 接口
public class myDateConverter implements Converter<String, Date> {
    /**
     * converter 方法作用  String ----> date
     *               simpleDateFarmat
     * @param s 代表的是配置文件中 日期字符串 <value>2020-10-11</value>
     * @return ：当把转换好的Date作为Converter方法的返回值 Spring会自定注入到birthday属性进行注入
     *
     */
    public Date convert(String s) {
        Date date=null;
        try { 
            SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");
            date = sdf.parse(s);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
第二步：在Spring的配置文件中进行配置
	MyDateConverter对象创建出来
<bean id="myDateConverter" class="com.qing.converter.myDateConverter"></bean>
	类型转换器的注册
    目的：就是告诉Spring框架，我们所创建的myDateConverter是一个类型转换器
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set><ref bean="myDateConverter"></ref></set>
        </property>
    </bean>
```

conversionServiceFactoryBean  id属性名：一定要是：conversionService

<h3>后置处理Bean</h3>

```markdown
BeanPostProcessor作用：对Spring工厂所创建的对象，进行在加工
```

后置处理Bean运行原理分析

![image-20201113173319524](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201113173319524.png)

![image-20201114102946582](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201114102946582.png)

BeanPostProcessor的开发步骤

第一步：类 实现BeanPostProcessor接口

第二步：Spring的配置文件中进行配置
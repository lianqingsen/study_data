<h2>Spring---AOP编程</h2>

<h3>第一章、静态代理设计模式</h3>

AOP使用了23种设计模式中的静态代理设计模式

1.为什么需要代理设计模式

在JavaEE分层开发中，那个层次对于我们来讲最重要

```markdown
dao  ---> service  ---> controller

在JavaEE分层开发中，最为重要的是Service层
```

Service层中包含了哪些代码

```markdown
Service层中，核心共呢（几十行 上百行代码）+额外功能（附加功能）
1.	核心功能
	业务运算
	DAO调用
2.	额外功能
	1.	不属于业务
	2.	可有可无
	3.	代码量很小
```

问题：额外功能写在Service层好不好

- Service层的调用者角度（Controller）：需要在Service层书写额外功能（需要）
- 软件设计者：Service不需要额外功能

实现生活中解决方式

![image-20201115102659414](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201115102659414.png)

<h3>代理设计模式</h3>

概念：

```markdown
通过代理类，为原始类（目标）增加额外的功能
好处：利于原始（目标）类的维护
```

名词解释

```markdown
1.目标类   原始类
	指的是	业务类（核心功能	--->业务运算 DAO调用）
2.目标方法	原始方法
	指的是（原始类）中的方法	就是目标方法（原始方法）
3.额外功能（附加功能）
	日志	事务	性能
```

代理开发的核心要素

```markdown
代理类 = 目标类（原始类）+额外功能 +原始类（目标类）实现相同的接口
```

```java
//第一步：于原始类实现相同接口
public class UserServiceProxy implements UserService {
    //创建原始对象
    private UserServiceImpl userService=new UserServiceImpl();
    public void register(User user) {
        //额外功能
        System.out.println("----log----");
        userService.register(user);
    }
    public boolean login(String username, String password) {
        System.out.println("----log----");
        return userService.login(username, password);
    }
}
```

静态代理：每一个原始类都会手工编写一个代理类（.java .class）

**静态代理存在的问题**

```markdown
静态类文件数量过多，不利于项目管理
代码可维护性差
```

<h2>Spring的动态代理开发</h2>

**1.Spring动态代理的概念**

```markdown
通过代理类，为原始类（目标）增加额外的功能
好处：利于原始（目标）类的维护
```

搭建开发环境

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.1.14.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.8.8</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.3</version>
</dependency>
```

Spring动态代理开发步骤：

1.创建原始对象（目标对象）

```java
public class UserServiceImpl implements UserService {
    public void register(User user) {
        //因为register方法属于原始方法 只负责核心功能
        System.out.println("UserServiceImpl.register 业务运算+DAO调用");
    }
    public boolean login(String username, String password) {
        System.out.println("UserServiceImpl.login 业务运算+DAO调用");
        return true;
    }
}
```

在Spring配置文件中创建 原始对象

```xml
<bean id="userService" class="com.qing.proxy.UserServiceImpl"></bean>
```

2.额外功能

MethodBeforeAdvice接口

```markdown
额外的功能书写在接口的实现中，运行在原始方法执行之前运行额外功能
```

```java
public class Before implements MethodBeforeAdvice {
    /***
     * 作用：需要把运行原始方法执行之前的额外功能放在Before方法中
     * @param method
     * @param objects
     * @param o
     * @throws Throwable
     */
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("------Method Before Advice Log------");
    }
}
```

```xml
 <bean id="before" class="com.qing.dynamic.Before"></bean>
```

3.定义切入点

```markdown
切入点：额外功能的加入位置
目的：由程序员根据需求，决定额外功能加入给那个原始方法
```

```xml
简单测试：所有方法都作为切入点，都加入额外功能
<aop:config>
        <aop:pointcut id="b" expression="execution(* *(..))"/>
    </aop:config>
```

4.组装(2 3整合)

```xml
表达的含义：所有的方法都加入before的额外功能
<aop:advisor advice-ref="before" pointcut-ref="b"></aop:advisor>
```

5.调用

```markdown
目的：获得Spring工厂创建的动态代理对象，并进行调用
注意：
	1.Spring工厂通过原始对象的id值获得的是代理对象
	2.获得代理对象后，可以通过声明接口类型，进行对象的存储
```

**动态代理细节分析**

Spring创建的动态代理类在哪里？

Spring框架在运行时，通过`动态字节码`技术，在JVM中创建的，在JVM内部，等程序结束后，会和虚拟机一起消失

什么叫动态字节码：通过第三方动态字节码框架，在虚拟机中创建对应类的字节码，进而创建对象，当虚拟机结束，动态字节码跟着消失。 

结论：动态代理不需要定义类的文件，都是JVM运行过程中动态创建的，所有不会造成静态代理，类文件数量过多，影响项目管理的问题。

![image-20201115234555257](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201115234555257.png)

动态代理编程简化代理的开发

```markdown
在额外功能不改变的前提下，创建其他目标类（原始类）的代理对象那个时，只需要指定目标类（原始）对象即可。
```

动态代理额外功能维护性 大大增强了

Spring动态代理详解

```java
// method:额外功能所增加给的那个原始方法
//Object[]:额外功能所增加给的那个原始方法的参数
//Object:额外功能所增加给的那个原始对象
public void before(Method method, Object[] objects, Object o) throws Throwable  {

}
```

MethodInterecptor(方法连接器)

```java
public class Arround implements MethodInterceptor {
    /**
     * invoke方法的作用 :额外功能在书写在invoke
     *                额外功能    原始方法之前
     *                           原始方法之后
     *                           原始方法之前 之后
     * @param methodInvocation （Method）：额外功能所增加给的那个原始方法
     *                         login
     *                         register
     * @return Object: 原始方法执行之后的返回值
     * @throws Throwable
     */
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("-----额外功能 运行在原始方法之前-----");
        Object proceed = methodInvocation.proceed();
        System.out.println("-----额外功能 运行在原始方法之后-----");
        return proceed;
    }
}
```

什么样的额外功能 运行在原始方法执行之前，之后都要添加？

答：事务

额外功能运行在原始方法抛出异常的时候

2.切入点详解

```xml
切入点决定额外功能的加入位置（方法）
 <aop:pointcut id="b" expression="execution(* *(..))"/>
execution(* *(..)) --->匹配了所有方法

1.execution() 切入点函数
2.* *（..）	切入点表达式
```

<h2>切入点表达式</h2>

![image-20201116225950646](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201116225950646.png)

**第一种：方法切入点表达式**

```markdown
* *(..) --->所有方法

*	----> 修饰符 返回值
*	----> 方法名
()  ----> 参数表
..	----> 对于参数没有要求(参数有没有，餐宿和有几个都行，参数是什么类型的都行)
```

定义loging方法作为切入点

```xml
<aop:pointcut id="b" expression="execution(* login(..))"/>
```

，定义login方法login方法有两个字符串类型的参数 作为切入点

```xml
<aop:pointcut id="b" expression="execution(* login(String,String))"/>
#注意：对于非java.lang包中的类型，必须要写权限定名
..可以和具体的参数连用
* login(String, ..)
```

**上面所写的切入点表达式最大的缺点是匹配不精准**

精准方法切入点限定

```javascript
修饰符		返回值			包、类、方法（参数）
    *	com.qing.proxy.UserServiceImpl.login(..)
    *	com.qing.proxy.UserServiceImpl.login(String,String)
```

**第二种：类切入点**

```java
指定特定类作为切点（额外功能加入的位置）自然这个类的所有放，都会加上对应的额外功能
```

语法1

```xml
#类中的所有方法加入了额外功能
<aop:pointcut id="b" expression="execution(* com.qing.proxy.UserServiceImpl.*(..))"/>
```
语法2

```markdown
#忽略包
1.类只存在一级包 com.UserServiceImpl
* *.UserServiceImpl.*(..)
2.类存在多级包  com.qing.proxy.UserServiceImpl
* *..UserServiceImpl.*(..)
```

**第三种：包切入点表达式 更有实战价值**

```markdown
指定包作为额外功能能加入的位置，自然包中的所有类及其方法都会加入额外的功能
```

语法1

```markdown
#切入点包中的所有类，必须在proxy中，不能在proxy包的子包中
<aop:pointcut id="b" expression="execution(* com.qing.proxy.*.*(..))"/>
```

语法2

```markdown
#切入点当前包及其子包都生效
<aop:pointcut id="b" expression="execution(* com.qing.proxy..*.*(..))"/>
```

切入点函数

切入点函数：用于执行切入点表达式

1.execution

```markdown
最为重要的切入点函数，功能最全
执行 方法切入点表达式 类切入点表达式 包切入点表达式

弊端：exection执行切入点表达式，书写麻烦
 	execution(* com.qing.proxy..*.*(..))
注意：其他的切入点函数，简化是execution书写复杂度，功能上完全一致
```

2.args

```markdown
作用:主要用于函数的(方法)参数的匹配
切入点：方法参数必须得是2个字符串类型的参数
	execution(* *(String,String))
	args(String,String )
```

3.within

```markdown
作用：主要用于进行类、包切入点表达式的匹配
execution(* *..UserServiceImpl.*(..))
within(*..UserServiceImpl)
```

4.@annotation

```markdown
作用：为具有特殊注解的方法加入额外功能
<aop:pointcut id="b" expression="@annotation(com.qing.Log)"/>
```

切入点函数的逻辑运算

```markdown
指的是 整合多个切入点函数一起配合工作，进而完成更为复杂的需求
```

and 与操作

```markdown
案例：login 同时 参数2个字符串
execution(* login(String,String))
execution(* login(..)) and args(String,String)
注意：与操作不同用于同种类型的切入点函数
```

or 或操作

```markdown
案例：register方法与login方法作为切入点
execution(* login(..)) or execution(* register(..))
```

<h2>AOP编程</h2>

AOP概念

```markdown
AOP (Aspect Oriented Programing)面向切面编程=Spring动态代理开发
切面=	切入点	+  额外功能

OOP (Object Oriented Programing)面对对象编程
以对象为单位的程序开发，通过对象间的彼此协同，相互调用，完成程序的构建

ROP (Producer Oriented Programing)面对过程编程

AOP的概念：
	本质就是Spring的动态代理开发，通过代理类为原始方法增加额外功能
	好处：利于原始类的维护
注意：AOP编程不可能取代OOP，基于OOP编程
```

<h2>AOP的底层实现原理</h2>

**1.核心问题**

```markdown
1.AOP如何创建动态代理类（动态字节码技术）
2.Spring工厂如何加工创建代理对象
	通过原始类id，获取的是代理对象
```

**2.动态代理类的创建**

2.1 JDK的动态代理

类加载器的作用：classloader

```markdown
1.通过类加载器把对应字节码文件加载到JVM
2.通过类加载器创建类的Class对象，从而创建这个类的对象
User ---> user
User类class对象 -》new User()-> user
```

![image-20201117160131966](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201117160131966.png)

![image-20201117161829725](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201117161829725.png)

2.2 CGlib的动态代理

```markdown
Cglib创建动态代理的原理：通过父子继承关系创建代理对象，原始类作为父类，代理类作为子类。这样可以保证方法一致，同时代理类提供新的实现方法(额外功能+原始方法)
```



![image-20201118113949945](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201118113949945.png)

```java
public class testCglib {
    public static void main(final String[] args) {
        //1.创建原始对象
        final UserService user=new UserService();
        /*2.通过Cglib方式创建动态代理对象
        实例化Enhancer对象
        Enhancer.setClassLoader();
        Enhancer.superClass();
        Enhancer.setCallBack(); --->实现MethodInterecptor()接口
        Enhancer.create（）--->创建动态代理
         */
        Enhancer enhancer=new Enhancer();
        enhancer.setClassLoader(testCglib.class.getClassLoader());
        enhancer.setSuperclass(user.getClass());
        MethodInterceptor interceptor=new MethodInterceptor() {
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                System.out.println("------log------");
                Object invoke = method.invoke(user,objects);
                return invoke;
            }
        };
        enhancer.setCallback(interceptor);
        UserService userService = (UserService) enhancer.create();
        userService.login("张三", "123");
        userService.register(new User());
    }
}
```

总结：

```markdown
JDK动态代理  Proxy.newProxyInstance()  通过接口创建代理的实现类
Cglib动态代理 Enhancer                通过继承父类创建的代理类
```

Spring工厂如何加工原始对象

![image-20201118125611845](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201118125611845.png)

**基于注解的AOP编程**

基于注解的AOP编程的开发步骤

1.原始对象

2.额外功能

3.切入点

4.组装

```java
通过切面类 定义了 额外功能 @Around
		定义了  @Around("execution(* login(..))")
		@Aspect 切面类
@Aspect
/**
 *          1.额外功能
 *              public class Arround implements MethodInterceptor {
 *              public Object invoke(MethodInvocation methodInvocation) throws Throwable {
 *              System.out.println("-----额外功能 运行在原始方法之前-----");
 *              Object proceed = methodInvocation.proceed();
 *              System.out.println("-----额外功能 运行在原始方法之后-----");
 *              return proceed;
 *     }
 * }
 *          2.切入点
 */
public class MyAspcet {
    @Around("execution(* login(..))")
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("----log----");
        Object ret = joinPoint.proceed();
        return ret;
    }
}
```

```xml
    <bean id="userService" class="com.qing.aspect.UserServiceImpl"></bean>
    <!--组装切面-->
    <bean id="arround" class="com.qing.aspect.MyAspcet"></bean>
    <!--告诉Spring基于注解进行AOP编程-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

细节：

```java
切入点复用:在切面类中定义一个函数，上面@Pointcut注解，通过这种方式，定义切入点表达式，后续更加利于切入点复用。
 
public class MyAspcet {
    @Pointcut("execution(* login(..))")
    public void myPointCut(){}

    @Around(value = "myPointCut()")
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("----arround log----");
        Object ret = joinPoint.proceed();
        return ret;
    }
    @Around(value = "myPointCut()")
    public Object arround1(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("----arround tx----");
        Object ret = joinPoint.proceed();
        return ret;
    }
}

```

基于解决的动态代理创建方式

```markdown
AOP底层实现， 2种代理创建方式
	1.JDK  通过实现接口 做新的实现类方式 创建代理对象
	2.Cglib 通过继续父类，做新的子类  创建代理对象
默认情况 AOP编程 底层应用JDK动态代理创建方式

```

**AOP开发种的一个坑**

```java
坑：在同一个业务类种，进行业务方法间的相互调用，只有外层的方法，才是加入额外功能，（内部的方法，通过普通的方式调用，都用的是原始方法）如果想让内层的方法也调用代理的对象方法，ApplicationContedxtAware获得工厂，通过工厂调用代理对象
    
    //Spring工厂属于重量级资源，一个应用中，应该只创建一个对象
public class UserServiceImpl implements UserService, ApplicationContextAware {
    private ApplicationContext context;
    public void register(User user) {
        System.out.println("UserService.register");

    }

    public void login(String username, String password) {
        System.out.println("UserService.login");
        UserService userService = (UserService) context.getBean("userService");
        userService.register(new User());
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.context=applicationContext;
    }
}
```

**AOP阶段知识总结。**

![image-20201120085847238](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201120085847238.png)
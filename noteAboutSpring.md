# Spring

- Spring框架概述
- 入门案例：IoC
- 入门案例：DI
- 装配Bean基于XML
  - 实例化方式
    - 3种bean实例化方式：
      - 默认构造
         ```java
         <bena id="" class=""> //必须提供默认构造
         ```
      - 静态工厂
        - 常用于Spring整合其他框架（工具）
        - 用于生成实例对象，所有方法必须是static
        ``` java
         <bean id="" class="工厂全限定类名" factory-method="静态方法">
        ``` 
        - 首先需要配置工厂
        - 然后在applicationContext里写工厂的bean
      - 实例工厂
        - 必须现有工厂的实例对象，通过实例对象创建对象。提供所有的方法必须都是非静态的
          ```xml
          <!-- 创建工厂实例 -->
          <bean id="myBeanFactoryId" class=""></bean>
          <!-- 获得userService -->
          <bean id="userServiceId" facorty-bean="myBeanFactoryID" factory-method=""></bean>
          ```
      - Bean种类
        - 普通bean:之前操作的都是普通bean.
            ```java
            <bean id="" class="A"> //spring创建A实例，并返回
            ```
        - FactoryBean:是一个特殊的Bean，具有工厂生成对象的能力，只能生成特定的对象。
          - bean必须实现FactoryBean的接口，此接口提供方法getObject()用于获得特定的bean
          ```java
          <bean id="" class="FB">//先创建FB实例，使用getObject()方法，并返回方法的返回值
                FB fb = new FB();
                return fb.getObject();
          ``` 
          - BeanFactory和FactoryBean对比?
            - BeanFactoy:工厂，生产任意Bean
            - FacoryBean：特殊的bean，用于生成另一个特定的Bean，此工厂bean用于生产代理.
              ```java
                <bean id="" class="....ProxyFactoyBean">//获得代理对象实例，AOP使用
              ``` 
  - 作用域
    - 用于Spring创建的bean的实例的个数
      - singleton
      - prototype
      - request
      - session
      - globalSession
    - 取值:
      - singleton 单例:默认值
      - prototype 多例：每执行一次getBean将获得一个实例。例如:struts整合spring，配置action多例
    - 配置信息
      ```java
        <bean id="" class="" scope="">
      ``` 
  - 生命周期(11步骤)
    - 初始化和销毁
      - 目标方法执行前后执行，将进行初始化或销毁
      ```java
        <bean id="" class="" init-method="初始化方法名称" destory-method="销毁的方法名称">
      ``` 
    - BeanPostProcessor后处理Bean
      - spring提供的一种机制，只要实现此接口BeanPostProcessor,并将实现类提供给spring容器，spring容器将自动执行，在初始化方法前执行before()，在初始化方法后执行after()
      - spring提供的工厂钩子，用于修改实例对象，可以生成代理对象，是AOP的底层
      - 编写实现类；配置
  - 属性依赖注入
    - 依赖注入方式：手动装配和自动装配
      - 手动装配：一般进行配置信息都采用的是手动
        - 基于xml装配：构造方法，setter方法
          - 构造方法注入
            ```xml
            <!-- <constructor-arg>用于配置构造方法一个参数argument 
            name:参数名称
            value：设置普通数据
            ref：引用数据，一般是另一个bean的id值

            index：参数的索引号，从0开始。如果只有索引，匹配多个构造方法时，默认使用第一个
            type:确定参数类型
            -->
            <constructor-arg name="参数名称" vaule="普通数据" ref="引用数据（一般是另一个bean的id值）">
            ``` 
          + setter方法
            ```xml
            <!-- 
                setter方法注入
                ·普通数据
                <property name="" value="值">
                等效
                <property name="">
                    <value=>值
                ·引用数据
                <property name="" ref="另一个bean">
                等效
                <property name="">
                    <ref bean="另一个bean">
            -->
            ```    
          + p命令空间
            - 对setter方法的一种简化，替换```<property>```,而是在``` <bean p:属性名="普通值"  p:属性名-ref="引用值"> 
            - p命名空间使用的前提，必须添加命名空间
        - 基于注解装配
      - 自动装配：struts和spring整合可以装配
      + 集合注入
        ```xml
        <!-- 
            集合的注入都是给<property>添加子标签
                数组:<array>
                List:<list>
                Set:<set>
                Map:<map> 存放键值对，一般用<entry>
                Properties:<properties>

            普通数据:<value>
            引用数据:<ref>
         -->
         <bean id="collDataId" class="">
            <property name="arrayData">
                <array>
                    <value></value>
                    <value></value>
                    <value></value>
                </array>
        ``` 
+ 装配Bean基于注解
  - 注解：就是一个类。使用@注解名称
  - 开发中：使用注解 取代xml配置文件。
  1. @Component 取代```<bean class="">```标签；
   @Component("id") 取代 ```<bean id="" class="">```
  2. web开发，提供3个@Component 注解衍生注解(功能一样)
    - @Repository:dao层
    - @Service:service层
    - @Controller:web层
  3. 依赖注入
    - 普通值:@Value("")
    - 引用值:
        1. 按照类型注入 @Autowired
        2. 按照名称注入1 @Autowired @Qualifier("名称")
        3. 按照名称注入2 @Resource("名称")
        4. 生命周期
           1. 初始化: @PostConstruct
           2. 销毁: @PreDestroy
        5. 作用域
           1. @Scope("prototy")
  - 注解使用前提，添加命名空间，让spring扫描含有注解类
- 整合Junit
- 整合WEB

## Sping编写流程（基于xml）
1. 导入jar包：4+1->beans/core/context/expression  |commons-logging
2. 编写目标类:dao和service
3. spring配置文件:
   - IoC:```<bean id="" class="">```
   - DI:```<bean><property name="" value=""|ref="">```
   - 实例化方式：
     - 默认构造
     - 静态工厂:```<bean id="" class="工厂类" factory-method="静态方法">```
     - 实例工厂:```<bean id="工厂id" class="工厂类" > <bean id="" factory-bean="工厂id" factory-method="方法">```
   - 作用域:```<bean id="" class="" scope="">```
   - 生命周期:```<bean id="" class="" init-method="" destroy-method="">```
   - 后处理bean:BeanPostProcessor接口:<bean clas="注册">，对容器中所有的bean都生效
   - 属性注入
     - 构造方法注入:```<bean><constructor-arg index="" type="">```
     - setter方法注入:```<bean><property>```
     - p命令空间:简化property ```<bean p:属性名="普通值" p:属性名-ref="引用值">```
     - 集合注入:
       - 数组:```<array>```
       - List
       - Set
       - Map:```<entry key="" value="">```
       - Properties
4. 核心API:
   1. BeanFactory:延迟实例化Bean，第一次调用getBean
   2. ApplicationContext 一般常用，功能更强
      - ClassPathXmlApplicationContext:加载classpath.xml文件
      - FileSystemXmlApplicationContext:加载指定盘符文件 

## 注解
1. 扫描含有注解的类
   - ```xml
      <context:component-scan base-package="...."> 
      ```
2. 常见的注解:
   - @Component 组件，任意的bean
   - WEB:
     - @Controller web成
     - @Service service层
     -  @Repository dao层
   - 注入->字段或setter方法
     - 普通值: @Value
     - 引用值:
       - 类型:@Autowired
       - 名称1:@Autowired @Qualifier("名称")
       - 名称2:@Resource("名称")
   - 作用域
     - @Scope("prototy")
   - 生命周期
      1. 初始化: @PostConstruct
      2. 销毁: @PreDestroy


# AOP面向切面
- AOP介绍
  - 什么是AOP
  - AOP实现原理
    - aop底层将采用代理机制进行实现
    - 经典应用:事务管理，性能监视，安全检查，缓存，日志
    - 接口+实现类:spring采用jdk的动态代理proxy
    - 实现类:spring 采用cglib字节码增强
  - AOP术语
    - Target:目标类，需要被代理的类,例如:UserService
    - Joinpoint:连接点，指那些可能被拦截的点(方法),例如:所有方法
    - PointCut:切入点，已经被增强的连接点。例如:addUser()
    - advice:通知/增强，增强代码,例如：before（），after（）
    - Weaving:织入，指吧增强advice应用到目标对象target来创建新的代理对象proxy的过程
    - proxy:代理
    - Aspect：切入点PointCut和通知advice的结合
- 手动方式
  - jdk动态代理（对"装饰者"设计模式的简化）
    1. 目标类:接口+实现类
    2. 切面类：用于通知MyAspect
    3. 工厂类：编写工厂类来生成代理
    4. 测试
  - cglib字节码增强
    - 没有接口，只有实现类
    - 采用字节码增强框架cglib，在运行时 创建目标类的子类，从而对目标类进行增强
    - 导入jar包
- AOP联盟通知
  - 环绕通知 org.aopalliance.intercept.MethodInterceptor
- spring编写代理:半自动
  - 让spring创建代理对象，从spring容器中手动获取代理对象。
  - 导入jar包
    - 核心:4+1
    - AOP:AOP联盟（规范），spring aop（实现）
- spring编写代理：全自动
  - 从spring容器获得目标类，如果配置了aop，spring将自动生成代理
  - 要确定目标类，aspectj 切入点表达式，导入jar包

## AspectJ
- 介绍
  - AspectJ是一个基于Java语言的AOP框架
  - spring2.0以后新增了对AspectJ切点表达式的支持
  - @AspectJ是AspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面
  - 主要用途：自定义开发
- 切入点表达式
  1. execution() 用于描述方法
     - 语法: execution(修饰符 返回值 包.类.方法（参数）throw 异常)
       - 修饰符: 一般省略
         - public 公共方法
         - ```*```      任意
       - 返回值
         - void 没有返回值
         - String 返回字符串
         - ``` * ``` 任意
       - 包，[省略]
         - 固定包
         - 目录.*.service  固定目录下任意子包
         - com.itheima.crm..  crm下所有子包
         - com.itheima.crm.*.service.. crm包下面任意子包固定目录service目录下的任意包
       - 类，[省略]
         - UserServiceImpl  指定类
         - *Impl            以Impl结尾
         - User*            以User开头
         - ```*```          任意
       - 方法
         - addUser  固定方法
         - add* 以add开头
         - *add 以add结尾
         - ```*``` 任意
       - （参数
         - () 无参
         - (int) 一个整形
         - (int int) 两个
         - (..) 参数任意
       - throw，可省略 一般不写
       - 综合：execution(* com.itheima.crm.*.service..*.*(..))
  2. bean(id) 对指定的bean所有的方法
     - bean("userServiceId") 
  3. AspectJ通知类型
     - aop联盟定义通知类型，具有固定接口，必须实现，从而确定方法名称
     - aspectj通知类型，只定义名称。以及方法格式
     - 个数:6种;知道5种，掌握一种
       - before
       - afterReturnning
       - around
         - 必须手动执行目标方法
       - afterThrowing
       - after
     - 导入jar包：4个
       - aop联盟
       - spring-aop
       - aspect规范
       - spring-aspect
- 基于xml
- 基于注解的学习
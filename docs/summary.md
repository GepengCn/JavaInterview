### SpringMVC 工作原理

--- 

简单来说就是客户端发送一次请求，SpringMVC怎么处理的

1. 客户端发送请求到**前端控制器**`DispatcherServlet`
2. 调用`HandlerMapping`,解析请求对应的Handler(即`Controller`)
3. 由`HandlerAdapter`负责处理`handler`请求，并执行`handler`里的业务逻辑
4. 返回一个`ModelAndView`对象,`Model`是返回的数据对象,`View`是个逻辑上的`View`
5. `ViewResolver`会根据`View`查找实际的`View`
6. `DispatcherServlet`会将`Model`填充到`View`
7. 把`View`返回给请求者



?> `DispatcherServlet`:SpringMVC入口,接收请求、响应结果，整个流程的控制中心。

?> `HandlerMapping`:根据请求的url查找对应Handler

?> `HandlerAdapter`:负责真正处理的`Handler`的适配器

?> `Handler`:实现业务逻辑的地方(Controller)，**需要自己实现**

?> `View resolver`:视图解析器，负责从逻辑视图中解析到真正的视图

?> `View`:实现前台展示,**需要自己实现**

### 说一下自己对 IOC 、AOP 的理解

---

#### Spring AOP


?> 一种面向切面的编程思想,将许多业务流程中的**通用功能或者说是系统功能**抽取出来，形成独立的切面，在合适的时机将切面切入到流程的指定位置中，分离应用的**业务逻辑**与**系统级功能**进行内聚性的开发，应用只负责实现**业务逻辑**而不必关心其它
   
!>  通知(`Advice`)

> 通知有5种类型：


- `Before`
在方法被调用之前调用


- `After`
在方法完成后调用通知，无论方法是否执行成功


- `After-returning`
在方法成功执行之后调用通知


- `After-throwing`
在方法抛出异常后调用通知


- `Around`
通知了好、包含了被通知的方法，在被通知的方法调用之前后调用之后执行自定义的行为

!> 切点（`Pointcut`)

定义了切面要发生的地方

!> 连接点（`Joint point`）

方法的执行点

!> 引入（`Introduction`）

引用允许我们向现有的类添加新的方法或者属性

!> 织入（`Weaving`）

将 `aspect` 和其他对象连接起来, 并创建 `adviced` `object` 的过程.
根据不同的实现技术, `AOP`织入有三种方式:

- 编译器织入, 这要求有特殊的`Java`编译器.

- 类装载期织入, 这需要有特殊的类装载器.

- 动态代理织入, 在运行期为目标类添加增强(`Advice`)生成子类的方式.
`Spring` 采用动态代理织入, 而`AspectJ`采用编译器织入和类装载期织入.

!> 目标对象(`Target`)

织入`advice`的目标对象. 目标对象也被称为`advised object`.

因为 `Spring` `AOP` 使用运行时代理的方式来实现 `aspect`, 因此 `adviced object` 总是一个代理对象(`proxied object`)

注意, `adviced object` 指的不是原来的类, 而是织入 `advice` 后所产生的代理类.

!> AOP proxy

一个类被 `AOP` 织入 `advice`, 就会产生一个结果类, 它是融合了原类和增强逻辑的代理类.

在 `Spring AOP` 中, 一个 `AOP` 代理是一个 `JDK` 动态代理对象或 `CGLIB` 代理对象.

#### IOC

?> Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

!> **谁控制谁，控制什么**：传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对象的创建；**谁控制谁？**当然是IoC 容器控制了对象；**控制什么？**那就是主要控制了外部资源获取（不只是对象包括比如文件等）。

!> **为何是反转，哪些方面反转了**：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；**为何是反转？**因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；**哪些方面反转了？**依赖对象的获取被反转了。

### 深入解析spring中用到的九种设计模式

!> 第一种：简单工厂

?> 又叫做静态工厂方法（StaticFactory Method）模式，但不属于23种GOF设计模式之一。 
简单工厂模式的实质是由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。 
spring中的BeanFactory就是简单工厂模式的体现，根据传入一个唯一的标识来获得bean对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

如下配置，就是在 HelloItxxz 类中创建一个 itxxzBean。


```xml
<beans>
    <bean id="singletonBean" class="com.itxxz.HelloItxxz">
        <constructor-arg>
            <value>Hello! 这是singletonBean!value>
        </constructor-arg>
   </ bean>
 
    <bean id="itxxzBean" class="com.itxxz.HelloItxxz"
        singleton="false">
        <constructor-arg>
            <value>Hello! 这是itxxzBean! value>
        </constructor-arg>
    </bean>
 
</beans>
```

!> 第二种：工厂方法（Factory Method）

?> 通常由应用程序直接使用new创建新的对象，为了将对象的创建和使用相分离，采用工厂模式,即应用程序将对象的创建及初始化职责交给工厂对象。
   一般情况下,应用程序有自己的工厂对象来创建bean.如果将应用程序自己的工厂对象交给Spring管理,那么Spring管理的就不是普通的bean,而是工厂Bean。
   
以工厂方法中的静态方法为例讲解一下：

```java
import java.util.Random;
public class StaticFactoryBean {
      public static Integer createRandom() {
           return new Integer(new Random().nextInt());
       }
}
```

建一个config.xm配置文件，将其纳入Spring容器来管理,需要通过factory-method指定静态方法名称

```xml
<bean id="random"
class="example.chapter3.StaticFactoryBean" factory-method="createRandom" <!--createRandom方法必须是static的,才能找到 scope="prototype"-->
/>
```

测试:

```java
public static void main(String[] args) {
      //调用getBean()时,返回随机数.如果没有指定factory-method,会返回StaticFactoryBean的实例,即返回工厂Bean的实例       
      XmlBeanFactory factory = new XmlBeanFactory(new ClassPathResource("config.xml"));       
      System.out.println("我是IT学习者创建的实例:"+factory.getBean("random").toString());
}
```

!> 第三种：单例模式（Singleton）

?>保证一个类仅有一个实例，并提供一个访问它的全局访问点。 
`Spring`中的单例模式完成了后半句话，即提供了全局的访问点`BeanFactory`。但没有从构造器级别去控制单例，这是因为`Spring`管理的是任意的java对象。 

核心提示点：`Spring`下默认的`Bean`均为`Singleton`，可以通过`Singleton=“true|false”` 或者 `scope=“?”`来指定

!> 第四种：适配器（Adapter）

?> 将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

在`Spring`的体现： `Spring AOP` 模块对 `BeforeAdvice`、 `AfterAdvice`、 `ThrowsAdvice` 三种通知类型的支持实际上是借助适配器模式来实现的， 这样的好处是使得框架允许用户向框架中加入自己想要支持的任何一种通知类型， 上述三种通知类型是 `Spring AOP` 模块定义的， 它们是 `AOP` 联盟定义的 `Advice` 的子类型。
在`Spring`中 基本`Adapter`结尾都是适配器。

详细内容可以参考:
[spring常用模式--------适配器模式](https://www.jianshu.com/p/43bfb4516dd8)

!> 第五种：包装器（Decorator）

?> 动态地给一个对象添加一些额外的职责。就增加功能来说，`Decorator`模式相比生成子类更为灵活。

![summary_2](https://img-blog.csdnimg.cn/20181109164811116.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3psMXpsMnpsMw==,size_16,color_FFFFFF,t_70)

`Spring`中用到的包装器模式在类名上有两种表现：一种是类名中含有`Wrapper`，另一种是类名中含有`Decorator`。基本上都是动态地给一个对象添加一些额外的职责。

!> 第六种：代理（Proxy）

?> 为其他对象提供一种代理以控制对这个对象的访问。
   从结构上来看和`Decorator`模式类似，但`Proxy`是控制，更像是一种对功能的限制，而`Decorator`是增加职责。

![summary_3](https://img-blog.csdnimg.cn/20181109164833288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3psMXpsMnpsMw==,size_16,color_FFFFFF,t_70)

`Spring`的`Proxy`模式在`AOP`中有体现，比如`JdkDynamicAopProxy`和`Cglib2AopProxy`。 

!> 第七种：观察者（Observer）

?> 定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

![summary_4](https://img-blog.csdnimg.cn/2018110916485740.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3psMXpsMnpsMw==,size_16,color_FFFFFF,t_70)

`Spring`中`Observer`模式常用的地方是`listener`的实现。如`ApplicationListener`。


!> 第八种：策略（Strategy）

?> 定义一系列算法，将每一个算法封装起来，并使它们可互换。策略允许算法独立于使用它的客户端。

![summary_5](https://upload-images.jianshu.io/upload_images/13417101-317fa3140fbf4833.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/639/format/webp)

`Spring`中的体现：`Spring` 中在实例化对象的时候用到 `Strategy` 模式， 在`SimpleInstantiationStrategy` 有使用

详细内容可以参考:
[Spring常用模式--------策略模式](https://www.jianshu.com/p/3ea48ecd7178)

!> 第九种：模板方法（Template Method）

?> 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

![summary_6](https://upload-images.jianshu.io/upload_images/13417101-88000fb5ec0af1b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/404/format/webp)

应用场景： `Spring ORM` 数据模型，工作流
   
详细内容可以参考:

[spring常用模式--------模板模式](https://www.jianshu.com/p/011ca7232a26)

### Spring的4种事务特性，5种隔离级别，7种传播行为

**什么是事务:** 

?> 事务逻辑上的一组操作,组成这组操作的各个逻辑单元,要么一起成功,要么一起失败.

**事务特性（4种）:** 

- **原子性 （atomicity）**:强调事务的不可分割. 
- **一致性 （consistency）**:事务的执行的前后数据的完整性保持一致. 
- **隔离性 （isolation）**:一个事务执行的过程中,不应该受到其他事务的干扰 
- **持久性（durability）**:事务一旦结束,数据就持久到数据库

**如果不考虑隔离性引发安全性问题: **

- **脏读**:一个事务读到了另一个事务的未提交的数据 
- **不可重复读**:一个事务读到了另一个事务已经提交的 update 的数据导致多次查询结果不一致. 
- **虚幻读**:一个事务读到了另一个事务已经提交的 insert 的数据导致多次查询结果不一致.

**解决读问题: 设置事务隔离级别（5种）**
 
- **DEFAULT**:这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别. 
- **未提交读（read uncommited）**:脏读，不可重复读，虚读都有可能发生 
- **已提交读 （read commited）**:避免脏读。但是不可重复读和虚读有可能发生 
- **可重复读 （repeatable read）**:避免脏读和不可重复读.但是虚读有可能发生. 
- **串行化的 （serializable）**:避免以上所有读问题. 


**事务传播机制（7种）**

?> 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。在TransactionDefinition定义中包括了如下几个表示传播行为的常量：
   
**支持当前事务的情况：**

- TransactionDefinition.PROPAGATION_REQUIRED： 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- TransactionDefinition.PROPAGATION_SUPPORTS： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- TransactionDefinition.PROPAGATION_MANDATORY： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

**不支持当前事务的情况：**

- TransactionDefinition.PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- TransactionDefinition.PROPAGATION_NOT_SUPPORTED： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- TransactionDefinition.PROPAGATION_NEVER： 以非事务方式运行，如果当前存在事务，则抛出异常。

**其他情况：**

- TransactionDefinition.PROPAGATION_NESTED： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

[Spring的事务传播机制实例](https://www.jianshu.com/p/25c8e5a35ece)

### LRU

[浅析LRU(K-V)缓存](https://www.cnblogs.com/wxisme/p/4889846.html)


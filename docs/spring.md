### AOP
AOP思想的实现一般都是基于 代理模式 ，在`JAVA`中一般采用`JDK`动态代理模式，但是我们都知道，`JDK`动态代理模式只能代理接口而不能代理类。因此，`Spring AOP` 会这样子来进行切换，因为`Spring AOP` 同时支持 `CGLIB`、`ASPECTJ`、`JDK`动态代理。

- 如果目标对象的实现类实现了接口，`Spring AOP` 将会采用 `JDK` 动态代理来生成 AOP 代理类；
- 如果目标对象的实现类没有实现接口，`Spring AOP` 将会采用 `CGLIB` 来生成 `AOP` 代理类——不过这个选择过程对开发者完全透明、开发者也无需关心。

### [静态代理、JDK动态代理、CGLIB动态代理讲解](http://www.cnblogs.com/puyangsky/p/6218925.html)

>我们知道AOP思想的实现一般都是基于 代理模式 ，所以在看下面的文章之前建议先了解一下静态代理以及JDK动态代理、CGLIB动态代理的实现方式。

### [Spring AOP 入门](https://juejin.im/post/5aa7818af265da23844040c6)

### [IOC](https://www.cnblogs.com/wang-meng/p/5597490.html)

### [事务](https://juejin.im/post/5b00c52ef265da0b95276091)


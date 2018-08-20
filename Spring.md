## Spring

> 一个分层的JavaEE开发的一站式开源框架。

![](./resources/spring_framework.png)

> IOC:控制反转，将对象的创建权反转给Spring管理
>
> DI:依赖注入，前提必须有IOC环境，Spring管理这个类的时候，将这个类的依赖属性注入进来。

### 简单使用

* 编写Dao类和Dao实现类

```java

public interface UserDao {
    void save();
}

public class UserDaoImpl implements UserDao {

    @Override
    public void save() {
        System.out.println("save");
    }
}

```

* `applicationContext.xml`配置bean

```
<bean id="userDao" class="com.eamon.spring.UserDaoImpl"></bean>

```

* 编写测试类

```
// 创建工厂
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
UserDao dao = (UserDao)context.getBean("UserDao");
dao.save();
```

### Spring工厂类

* 结构图

![](./resources/spring_factory.png)

* ApplicationContext继承BeanFactory
* ApplicationContext加载配置文件时会将Spring管理的类都实例化。
* ApplicationContext实现类
	* **ClassPathXmlApplicationContext**: 加载类路径下的配置文件
	* **FileSystemXmlApplicationContext**:加载文件系统下的配置文件

### Bean的相关配置

* **id**: 使用了约束中的唯一约束，里面不能出现特殊字符。
* **name**: 没有使用约束中的唯一约束。里面可以出现特殊字符。
* **init-method**: Bean被初始化的时候执行的方法。
* **destory-method**: Bean被销毁的时候执行的方法。
* **scope**: Bean的作用范围
	* singleton: 默认，Spring会采用单例模式来创建这个对象
	* prototype: 多例模式。
	* request: 应用在web项目中, Spring创建这个类以后，将这个类存入request范围中
	* session: 应用在web项目中, Spring创建这个类以后，将这个类存入session范围中
	* globalsession: 应用在web项目中，必须在porlet环境下使用。但是如果没有这种环境，相对于session。

### Spring的属性注入

##### 构造方法的方式属性注入

```xml
<bean id="user" class="com.eamon.spring.User">
	<constructor-arg name="name" value="value"/>
</bean>

```

##### set方法的方式属性注入

```xml
<bean id="user" class="com.eamon.spring.User">
	<property name="name" value="张三"/>
	<property name="age" value="12" />
</bean>

```

#####  set方法设置对象类型的属性

```xml
<!-- ref：其它类的id或name-->
<bean id="user" class="com.eamon.spring.User">
	<property name="name" value="张三"/> 
	<property name="product" ref="product"/>
</bean>
```
#####  P名称控件的属性注入（Spring2.5+）

```xml
<bean
	...
	xmlns:p="http://www.springframework.org/schema/p">
	
	<!-- 使用p名称控件 -->
	<bean id="user" class="com.eamon.spring.User" p:name="张三" p:age="12"></bean>
	<bean id="product" class="com.eamon.spring.product" p:name="酱油" p:user-ref="user"></bean>

</bean>
```

##### SpEL属性注入（Spring3+）

```xml
<bean id="productInfo" class="com.eamon.spring.pdocutInfo" />

<bean id="product" class="com.eamon.spring.product">
	<property name="name" value="#{productInfo.name}"></property>
	<property name="price" value="#{productInfo.getPrice()}">
</bean>

<bean id="user" class="com.eamon.spring.User">
	<property name="name" value="#{"张三"}"/>
	<property name="product" value="#{product}"/>
</bean>

```

### Spring的IOC注解开发

#### 简单使用

* 导入相关的jar包
* 创建applicationContext.xml
* 配置主键扫描(哪个包下的类使用IoC注解)

```xml
<context:component-scan base-package=“com.eamon.spring” />
```
* 在类中添加注解

```
@Component(value="userDao")
// 相当于
<bean id="userDao" class="com.eamon.spring.userDaoImpl" />
```

#### 注解方式设置属性的值
* 如果有set方法，将属性注入的注解添加到set方法
* 如果没有set方法，将属性注入的注解添加的属性。

#### Spring的IoC注解

* **@Component:**

> 组件，用来修饰一个类，将这个类交给Spring管理。有三个衍生注解：
> @Controller(value="")： web层
> @Service(value="")： service层
> @Repository(value="")：dao层

* **@Value()**

> 设置普通属性的值

* **@Autowired**

> 设置对象类型的属性的值，默认按照类型完成属性注入。

* **@Qualifier(value="")**

> 与@Autowired一起用，完成按照名称属性注入

* **@Resource(name="")**

> 按照名称来完成属性注入，替代@Autowired+@Qualifier

* **@PostConstruct**

> 初始化方法，相当于init-method

* **@PreDestroy**

> 销毁方法，相当于destroy-method

* **@Scope**

> 作用范围，singleton，prototype等

#### IOC的XML和注解使用场景
* xml：可以适用任何场景。结构清晰，后期维护方便
* 注解：第三方库用不了。开发方便。

### Spring的AOP基于AspectJ注解开发

#### 简单使用

* 引入jar包
* 引入配置文件
* 编写目标并配置

```java
public interface UserDao {	
	void save()
}

public class UserDaoImpl implement UserDao {

	@override
	public void save() {
		System.out.println("save");
	}
}
```
```xml
<bean id="userDao" class="com.eamon.spring.demo.UserDaoImpl"
```

* 编写切面类并配置

```java
public class UserAspectAnno {

	public void before() {
		System.out.println("前置增强");
	}
}
```
```xml
<bean id="aspect" class="com.eamon.demo.UserAspectAnno"/>
```

### 使用注解的AOP对象目标类进行增强

* 在配置文件中打开注解的AOP开发

```
<aop:aspectj-autoproxy />
```

* 在切面类上使用注解

```
@Aspect
public class UserAspectAnno {
	
	@Before(value="execution(* com.eamon.spring.demo.UserDaoImpl.save())")
	public void before() {
		System.out.println("前置增强");
	}
}
```

### Spring的注解AOP的通知类型

* **@Before**

> 前置通知

```java
@Before(value="execution(* com.eamon.spring.demo.UserDaoImpl.save(..))")
public void before() {
	System.out.println("前置通知");
}
```

* **@AfterReturning**

> 后置通知

```java
@AfterReturning(value="com.eamon.spring.demo.UserDaoImpl.delete(..)", returning="result")
public void afterReturning(Object result) {
	System.out.println("后置通知:"+result)
}
```
* **@Around**

> 环绕通知

```java
@Around(value="execution(* com.eamon.spring.demo.UserDao.update(..))")
public void around(ProceedingJoinPoint joinPoint) throws Throwable {
	System.out.println("环绕前增强");
	Object obj = jointPoint.proceed();
	System.out.println("环绕后增强");
	return obj;
}
```

* **@AfterThrowing**

> 异常抛出通知

```java
@AfterThrowing(value="execution(* com.eamon.spring.demo.UserDao.find(..))", throwing="e")
public void afterThrowing(Throwable e) {
	System.out.println("异常抛出增强:"+ e.getMessage());
}
```

* **@After**

> 最终通知

```java
@After(value="execution(* com.eamon.spring.demo.UserDao.update(..))")
public void after() {
	System.out.println("最终增强");
}
```




	
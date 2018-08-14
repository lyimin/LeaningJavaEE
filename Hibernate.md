## Hibernate

> 持久层ORM（对象关系映射）框架。将Java中的对象与关系型数据库中的表简历一种映射关系，从而操作对象就可以操作数据库中的表。

### Hibernate常见配置

#### class标签的配置

> 该标签用来建立类与表的映射关系。
	
- name：类的全路径
- table：表名（类名与表名一致可省略）
- catalog：数据库名

#### id标签的配置

> 该标签用来建立类中的属性与表中的主键的对应关系。

- name：类中的属性名
- column：表中的字段名（类中的属性名与表中的字段名一致可省略）
- length：长度
- type：类型

#### property标签的配置

> 该标签用来建立类中的普通属性与表字段的对应关系

- name：类中的属性名
- column：表中的字段名
- length：长度
- type：类型
- not-null：设置非空
- unique：设置唯一

### 核心配置方式

#### 必须配置

- driver：驱动类
- url：路径
- username：用户名
- password：密码

#### 可选配置

- hibernate.show_sql：显示SQL
- hibernate.format_sql：格式化SQL
- hibernate.hbm2ddl.auto：自动建表
	- none：不适用hibernate的自动建表
	- create：如果数据库已经有表，删除原表，再创建，如果没有表，新建表（测试）
	- create-drop：如果数据库已经有表，删除原表，执行操作，删除这个表。如果没有表，新建表，使用完再删表（测试）
	- update：如果数据库中有表，使用原表，如果没有再新建（更新表结构）
	- validate：如果没有表，不会创建，只会使用数据库中的原表（校验映射和表结构）

### Hibernate核心API

#### Configuration配置对象

> 对Hibernate进行配置，加载核心配置文件和映射文件。

#### SessionFactory

SessionFactory接口负责初始化Hibernate。负责创建Session对象。内部维护了Hibernate的连接池和Hibernate的二级缓存。是线程安全对象，一个项目创建一个对象即可

#### Session对象

Session代表的是Hibernate与数据库的连接对象。Session接口负责执行被持久化对象的CRUD操作。要注意的是Session对象是非线程安全的。

##### 常用方法

- 保存

	```Serializable save(Object obj);```
	
	```java
		User user = new User()
		session.save()
	```

- 修改
	
	```void update(Object obj);```
	
	```java
	Session session = HibernateUtils.openSession();
	Transaction ts = session.beginTransaction();
	
	// 先查询，再修改
	User user = session.get(User.class, 1l);
	user.setName("张三");
	session.update(user);
	
	tx.commit();
	session.close();
	```

- 删除
	
	```void delete(Object obj);```
	
	```java
	
	Session session = HibernateUtils.openSession();
	Transaction ts = session.beginTransaction();
	
	// 先查询再删除
	User user = session.get(User.class, 1l);
	user.delete(user);
	
	tx.commit();
	session.close();
	
	```

- 保存或更新

	``` void saveOrUpdate(Object obj) 	```

	```java
	Session session = HibernateUtils.openSession();
	Transaction ts = session.beginTransaction();
	
	User user = new User();
	user.setUserId(3l);
	user.setName("张三");
	session.saveOrUpdate(user);
	
	tx.commit();
	session.close();
	```

- 查询
	```Query createQuery(String HQL)```
	```SQLQuery createSQLQuery(String SQL)```
	
	```T get(Class c, Serializable id);```
	```T load(Class c, Serializable id);```
	
get方法和load方法的区别
> get方法
> 
> 1.采用的是立即加载，执行到这行代码时立即发送SQL语句去查询
> 
> 2.查询后返回真实对象本身
> 
> 3.查询一个找不到的对象时，返回null
> 
> load方法
> 
> 1.采用的是延迟加载，执行到这行代码时不会发送SQL语句，在使用这个对象时才会发送SQL语句。
> 
> 2.查询后返回的是代理对象，利用Javassist技术产生的代理。
> 
> 3.查询一个找不到的对象时，返回ObjectNotFoundException

#### Transaction事务对象

- commit()
- rollback()

### 持久化编写规则

- 提供一个无参构造方法。原因：Hibernate底层使用
- 属性需要私有，私有属性提供public的set和get方法
- 持久化类提供唯一标识OID与数据库主键对应
- 持久化类中的属性尽量使用包装类来定义，例如：int -> Integer。原因：基本数据类型默认值是0，包装类的默认值是null
- 持久化类不要使用final进行修饰。原因：load方法的实现有关

### 主键生成策略

- 一般不允许手动设置主键。Hibernate提供多种主键生成策略
	- ncreament: hibernate中提供的自动增长，适合short,int,long类型的主键，单线程使用
	- identity: 适合short,int,long类型的主键，使用的是数据库底层的自动增强机制。
	- sequence: 适合short,int,long类型的主键，采用序列方式。(oracle支持序列)
	- uuid:适用string类型的主键，使用Hibernate中的随机方式生成字符串主键。
	- native:本地策略，可以在identity和sequence之间进行自动切换
	- assigned: hibernate放弃外键的管理，需要通过手动编写程序或用户自己设置
	- foreign（了解）: 外部的。一对一的管理映射下使用

### 持久化的三种状态

- 瞬时态：没有唯一标识OID，没有被session管理
- 持久态：有唯一标识OID，被session管理
- 脱管态：有唯一标识OID，没有被session管理

```java
Session session = HibernateUtils.openSession();
Transaction transaction = session.beginTransaction();

Customer customer = new Customer();
customer.setName("张三") // 瞬时态
Serializable id = session.save(customer) 	// 持久态

transaction.commit()	
session.close()

System.out.println(customer.getName())	// 托管态

```

### Hibernate的一级缓存

> Hibernate提供两种缓存策略，1级缓存和二级缓存。
> 
> 1级缓存：session级别的缓存，一级缓存声明周期与Session一致。
> 
> 2级缓存：sessionFactory级别的缓存，需要手动配置。（不使用了，被Redis替代）

### Hibernate设置事务

- Read uncommitted : 1
- Read committed : 2
- Repeatable read : 4
- Serializable : 8

xml配置

```xml
<property name="hibernate.connection.isolation">4</property>
```

保证连接对象是同一个connection

- 向下传递（DBUtils）
- 使用ThreadLocal对象，Hibernate框架内部已经绑定了ThreadLocal,sessionFactory中提供了一个getCurrentSession方法。

```xml
<property name="hibernate.current_session_context_class">thread</property>

// thread: Session对象的生命周期与本地线程绑定
// jta:Session对象的生命周期与JTA事务绑定
// managed: Hibernate委托程序来管理Session对象的生命周期
// 注意：不必使用close方法

```

### Hibernate的其它api

- Query

> 用于接收HQL语句，查询多个对象。

```java
	Query query = session.createQuery("from User");
	List list = query.list();
	...

```

- Criteria

> QBC:更加面向对象的查询方法。

```java
	Criteria criteria = session.createCriteria(User.class);
	criteria.add(Restrictions.eq("name", "张三"));
	List list = criteria.list();
	...
 
```

- SQLQuery

> 用于接收sql语句

```java
	SQLQuery query = session.createSQLQuery("SELECT * FROM User");
	List list = query.list();
	...
```

### Hibernate一对多关联映射

> 建表原则：在多的一方创建外键指向一的一方的主键。

#### 配置多对一的映射关系

```xml
<many-to-one name="" class="" column="" cascade="save-update,delete"></many-to-one> 
```

- name: 一的一方对象的属性名
- class: 一的一方类的路径
- column：多的一方表的外键名称
- cascade: 级联操作

#### 配置一对多的映射关系

```xml
<set name="" cascade="save-update,delete" inverse="true">
	<key column=""></key>
	<one-to-many class=""></one-to-many>
</set>
```

- name: 多的一方对象集合的属性名
- column: 多的一方外键的名称
- class: 多的一方类的全路径
- cascade: 级联操作
- inverse: 放弃外键维护权

> 级联操作：操作一个对象的时候，是否同时操作其关联的对象
>
> 解决多余sql语句，单项维护：使一的一方放弃外键维护权。

### Hibernate 多对多关联映射

> 建表原则：创建一个中间表，中间表至少有两个字段分别作为外键指向多对多双方的主键

> 多对多关系需要使用中间表

```xml
<set table="">
	<key column="" />
	<many-to-many class="" column=""/>
</set>
```
- table: 中间表的表名
- key-column: 中间表对应的外键名
- class: 类的全路径
- column: 类的中间表的外键名称


### Hibernate查询方式

#### OID查询
> Hibernate根据对象的OID(主键)进行检索

- 使用get方法

```java
User user = session.get(User.class, 1l);
```

- 使用load方法

```java
User user = session.load(User.class, 1l);
```

#### 对象导航检索
> Hibernate根据一个已经查询到的对象，获得其关联的对象的一种查询方式。

```java
User user = session.get(User.class, 1l)
Product product = user.getProduct()
```

#### HQL检索

> Hibernate Query Language，Hibernate的查询语言，是一种面向对象的方式的查询语言，语法类似SQL。通过session.createQuery()，用于接收一个HQL进行查询方式。

- 简单查询

```java
Query q = session.createQuery("from User")
List<User> list = q.list()
...
```

- 别名查询

```java
Query q = session.createQuery("from User u");
List<User> = q.list();
```

- 排序查询

```java
List<User> list = session.createQuery("from User order by userId desc").list();
```

- 条件查询
	- 按位置绑定

	```java
	Query q = session.createQuery("from User where name=?")
	q.setParameter(0, "张三");
	List<User> list = q.list();
	...
	```	
	- 按名称绑定
	
	```java
	Query q = session.createQuery("from User where name=:n and age like :a");
	q.setParameter("n", "张三");
	q.setParameter("a", "1%");
	List<User> list = q.list();
	...
	```
- 投影查询
> 查询对象的某个或某些属性

```java
List<Object> list = session.createQuery("select u.name from User u").list()
List<Object[]> list = session.createQuery("select u.name,u.age from User u").list()
List<User> list = session createQuery("select new User(name, age) from User").list()
```

- 分页查询

```java
Query q = session.createQuery("from User")
q.setFirstResult(0)
q.setMaxResult(10)
List<User> list = q.list()
...
```

- 分组统计

```java
Object object = session.createQuery("select count(*) from User").uniqueResult()

List<Object[]> list = session.createQuery("select age,count(*) from User group by age having count(*) >= 2").list()
```

#### QBC检索
> Query By Criteria，条件查询。是一种更加面向对象化的查询的方式。

- 简单查询

```java
Criteria criteria = session.createCriteria(User.class)
List<User> list = criteria.list()
```

- 排序查询

```java
Criteria criteria = session.createCriteria(User.class)
	List<User> list = criteria.addOrder(Order.desc("age")).list()
```

- 分页查询

```
Criteria criteria = session.createCriteria(User.class)
criteria.setFirstResult(0)
criteria.setMaxResult(10)
List<User> list = criteria.list()
```

- 条件查询

```java
/*
=	eq
> 	gt
>= 	ge
< 	lt
<= 	le
!= 	ne
like
in
and
or
*/

Criteria criteria = session.createCriteria(User.class)
criteria.add(Restrictions.eq("age", "10"));
List<User> list = criteria.list() 
```

- 统计查询

```java
Criteria criteria = session.createCriteria(User.class);
criteria.setProjection(Projections.rowCount());
Long num = (Long) criteria.uniqueResult();
```

- 离线条件查询DetachedCriteria  (脱离session使用)

```java
DetachedCriteria dc = DetachedCriteria.forClass(User.class)
dc.add(Restrictions.like("name","张%"))；
	
// 绑定session
Criteria criteria = dc.getExecutableCriteria(session)
```

#### SQL检索
> 通过使用sql语句进行查询

### Hibernate的抓取策略

#### 延迟加载
> lazy（懒加载）。执行到该行代码的时候，不会发送语句去进行查询，在真正使用这个对象的属性的时候才会发送SQL语句进行查询。

- 类级别的延迟加载
	- 指的是通过load方法查询某个对象的时候，是否采用延迟。
	- 类级别延迟加载通过<class>上的lazy进行配置，如果让lazy失效
		- 将lazy设置为false		- 将持久化类使用final修饰		- Hibernate. Initialize()
- 关联级别的延迟加载
	- 指的是在查询到某个对象的时候，查询其关联的对象的时候，是否采用延迟加载。
	

#### 抓取策略
> 通过一个对象抓取到关联对象需要发送SQL语句，SQL语句如何发送，发送成什么样格式通过策略进行配置。
>  
> 通过<set>或者<many-to-one>上通过fetch属性进行设置
> 
> fetch和这些标签上的lazy如何设置优化发送的SQL语句

#### `<set>`上的fetch和lazy

- fetch：抓取策略，控制SQL语句格式
	- select：默认值，发送普通的select语句，查询关联对象	- join：发送一条迫切左外连接查询关联对象	- subselect：发送一条子查询查询其关联对象

- lazy：延迟加载，控制查询关联对象的时候是否采用延迟
	- true			：默认值，查询关联对象的时候，采用延迟加载	- false			：查询关联对象的时候，不采用延迟加载	- extra		：及其懒惰。
	#### `<many-to-one>`上的fetch和lazy- fetch	：抓取策略，控制SQL语句格式。	- select	：默认值，发送普通的select语句，查询关联对象。	- join		：发送一条迫切左外连接。
- lazy	：延迟加载，控制查询关联对象的时候是否采用延迟。
	- proxy	：默认值，proxy具体的取值，取决于另一端的<class>上的lazy的值。	- false		：查询关联对象，不采用延迟。	- no-proxy	：（不会使用）
## 一、MySQL
	
### ER关系图

- 实体： 方框
- 属性： 椭圆
- 关系： 菱形

### MySQL启动和关闭
- 启动:```mysql.service start```
- 关闭:```mysql.server stop```

### 数据库乱码问题
更改my.ini配置为 utf-8, gbk 等

### delete和truncate删除数据有什么差别

- delete: dml， 一条一条删除表中数据
- truncate: ddl， 先删除表再重建

### 去除重复的数据（DISTINCT）
```SELECT name DISTINCT FROM user```

### 别名（AS）
```SELECT u.name,u.age FROM user AS u```
```SELECT name AS 姓名, age AS 年龄 FROM user```

### 条件关键字（WHERE）
``` >、>=、<、<=、!=或<>、= ```

### 范围（BETWEEN…AND）
```SELECT * FROM user WHERE age BETWEEN 10 AND 20```

### 逻辑运算（AND OR NOT）

### 模糊查询（LIKE）
	
- _: 代表一个字符。
- %: 代表多个字符。

查询`name`字段的第`2`个字符为`好`的数据
``` SELECT * FROM user WHERE name LIKE '_好%' ```

查询`name`字段有`张`字符的数据
```SELECT * FROM user WHERE name LIKE '%张%' ```

### 范围查询（IN）

查询`age`字段值为10，15的数据
```SELECT * FROM user WHERE age IN (10,15)```

### 排序（ORDER BY）

- ASC: 升序，默认
- DESC: 降序

```
// 升序 
SELECT * FROM user ORDER BY age ASC
// 降序 
SELECT * FROM user ORDER BY age DESC
```

### 聚合函数
- sum(): 求和
- avg(): 求平均值
- count(): 求总数
- max(): 最大值
- min(): 最少值

### HAVING与WHERE区别
- having: 可以在分组之后接聚合函数
- where: 在分组前才可以接聚合函数

### 编写顺序
``` select … from … where … group by … having … order by ```

### 外键约束（FOREIGN KEY）
在`user`表中添加一个`product`表的`pid`的外键约束，并且命名为`pid`。SQL：
```ALTER TABLE user ADD FOREIGN KEY(pid) REFERENCES product(pid)```

### 多对多关系解决方案

> 多建一张中间表，将多对多的关系拆成一对多的关系，中间表至少要有两个外键：这两个外键分别指向原来的表

### 18、多表查询

- 交叉查询
- 连接查询
	- 内连接（inner join）:查询结果为两个表有关联的部分
		- 显示内连接
		- 隐式内连接
	- 外连接
		- 左外连接（left outer join）:查询结果为左表全部内容+右表有关联的部分
		- 右外连接（right outer join）:查询结果为右表全部内容+左表有关联的部分
- 子查询

```
// 隐式内连接
SELECT * FROM user AS u, category AS c WHERE u.cid=c.cid
// 显式内连接
select * FROM user AS u INNER JOIN category AS c ON u.cid=c.cid

// 左外连接
select * from user left outer join category where user.uid = category.cid
// 右外连接
select * from category right outer join category where category.cid=user.uid
```

### 18、分页（LIMITE）
- 第一个参数：索引
- 第二个参数：显示个数

```SELECT * FROM user LIMIT 1,10```







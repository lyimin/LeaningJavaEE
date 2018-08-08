## 四、Cookie & Session

## 请求转发和重定向

- 重定向

```java
response.sendRedirect("login_success.html")
```

- 请求从转发

```java
request.getRequestDispatcher("login_success.html").forward(request, response)
```

### 重定向和请求转发不同点

- **返回的状态码不同**。重定向（302），请求转发（200）
- **请求次数不同**。重定向请求次数最少有两次，服务器在第一次请求后，返回302以及一个url,浏览器根据这个url执行第二次访问。请求转发请求次数只有一次
- **重定向效率稍微低一点**，执行两次请求
- **重定向不能使用上一次request存储的数据**，因为是不同的两次请求，请求转发可以使用上一次的request对象

### Cookie

> 服务端给客户端的数据，并且存储在客户端，默认情况下，浏览器关闭就会清除
应用场景：自动登录，浏览记录，购物车

##### 简单使用
	
```java
// 添加cookie
Cookie cookie = new Cookie("key", "value")
response.addCookie(cookie)

// 获取cookie
Cookie []cookie = request.getCookies()

// 设置有效时间（单位秒）
cookie.setMaxAge(60 * 60 * 24 * 7)

// 指定域名才带上cookie 
cookie.setDomian(".baidu.com")

// /cookieDemo 这个路径才带cookie
cookie.setPath(“/cookieDemo”)

```

##### Cookie的安全问题

> 由于Cookie会保存在客户端上，所以有安全隐患问题。  还有一个问题， Cookie的大小与个数有限制。 为了解决这个问题 ---> Session .

## Session

> 会话，Session是基于Cookie的一种会话机制。 Cookie是服务器返回一小份数据给客户端，并且存放在客户端上。 Session是，数据存放在服务器端。

##### 简单使用

```java
// 得到会话ID
String ID = session.getId();

// 存值
session.setAttribute(key, value);

// 取值
session.getAttribute(key);

// 移除值
session.removeAttribute(key);
```

##### session的创建与销毁

* 创建

> 调用getSession方法

* 销毁

> 会话时间过去。默认有效期是30分钟
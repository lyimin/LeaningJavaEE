## 三、Servlet

### 概述

> Java程序，运行在web服务器上，用于接受和响应用户端的请求。

### 生命周期
- init:创建servlet实例时，就执行该方法，且只会调用一次
- service: 用户端来请求就会执行该方法，可以被执行多次
- destroy: servlet销毁时执行该方法。(项目从tomcat中移除，或者关闭tomcat都会执行)

### ServletConfig

> 获取servlet的一些配置信息

```xml
<init-param>
	<param-name>address</param-name>
	<param-value>guangdong</param-value>
</init-param>
```

```java
ServletConfig config = getServletConfig();
String address = config.getInitParameter('address')

```

### ServletContext

> 每个项目只有一个servletContext

1.可以获取全局参数

```xml
<context-param>
	<param-name>address</param-name>
	<param-value>guangdong</param-value>
</context-param>
```

```java
ServletContext context = getServletContext()
String address = context.getInitParameter('address')
```

2.可以获取web应用的资源

```java
ServletContext context = getServletContext()
String path = context.getRealPath() // 绝对路径

InputStream is = context.getResourceAsStream('file:/config.properties') // 相对路径
```

3.存储数据，servlet间共享数据域对象

```java
ServletContext context = getServletContext()
context.setAttribute('count', 1)
context.getAttribute('count')
```

ServletContext创建与销毁

- 创建：服务器启动时，会为托管的每一个web程序创建一个servletContext对象
- 销毁：服务器移除托管，或关闭服务器

### HttpServletRequest

> 这个对象封装了客户端提交过来的一切数据。
	
1.可以获取客户端请求头信息

```java
Enumeration<String> names = request.getHeaderNames()
while(names.hasMoreElements()) {
	String name = names.nextElement();
	String value = request.getHeader(name)
}
```

2.获取客户端提交过来的数据

```java
String name = request.getParameter("name");
String address = request.getParameter("address");

// name=zhangsan&name=lisi&name=wangwu 一个key可以对应多个值。
Map<String, String[]> map = request.getParameterMap();
Set<String> keySet = map.keySet();
Iterator<String> iterator = keySet.iterator();
while (iterator.hasNext()) {
	String key = (String) iterator.next();
	String value = map.get(key)[0];
	String value1 = map.get(key)[1];
	String value2 = map.get(key)[2];
}

```

### HttpServletResponse

> 负责返回数据给客户端。

- 输出数据到页面上

```java
// 以字符流的方式写数据	
response.getWriter().write("<h1>hello response...</h1>");

// 以字节流的方式写数据
response.getOutputStream().write("hello response2222...".getBytes());

// 设置状态码
response.setStatus("200")

// 设置请求头
response.setHeader()

// 设置contentType
response.setContentType()
```

### 下载资源案例

> http://localhost:8080/DownloadDemo?filename=aa.jpg

```java
// 获取文件名
String filename = request.getParameter("filename");
// 获取文件路径
String path = getServletContext().getRealPath("res/"+filename);
// 以下载的方式打开
response.setHeader("Content-Disposition", "attachment", "filename="+filename);
// 转化为输入流
InputStream is = FileInputStream(path);
OutputStream os = response.getOutputStream();

int len = 0;
byte[] buff = new byte[1024];
while( (len = is.read(buff)) != -1) {
	os.write(buff, 0, len);
}

os.close();
is.close();

```


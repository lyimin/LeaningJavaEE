## 五、JSP

> 全称Java Server Page，是一个Java类，继承servlet，所以jsp其实就是一个servlet

### 简单用法

```jsp
<%@ 指令名称 %>

```

### page指令

* language

> 表明jsp页面中可以写java代码

* contentType

> 其实即使说这个文件是什么类型，告诉浏览器我是什么内容类型，以及使用什么编码

* pageEncoding 
 
> jsp内容编码
 
* extends 

>用于指定jsp翻译成java文件后，继承的父类是谁，一般不用改。

* import

> 导入使用的包，一般不用手写

* session

> 值可选的有true or false。用于控制在这个jsp页面里面，能够直接使用session对象。

* errorPage

> 指的是错误的页面， 值需要给错误的页面路径

* isErrorPage

> 上面的errorPage 用于指定错误的时候跑到哪一个页面去。 那么这个isErroPage , 就是声明某一个页面到底是不是错误的页面。

### include指令

> 引入另外一个jsp的内容进来。

```jsp
<%@ include file="other02.jsp"%>
```

### taglib指令
```jsp
<%@ taglib prefix=""  uri=""%>  

uri: 标签库路径
prefix : 标签库的别名 
```

### JSP 动作标签

#### jsp:include标签

> 包含指定的页面， 这里是动态包含。 也就是不把包含的页面所有元素标签全部拿过来输出，而是把它的运行结果拿过来。

```
<jsp:include page="other.jsp"></jsp:include>
```

#### jsp:forward

> 前往哪一个页面。请求转发

```
<jsp:forward page="other.jsp"></jsp:forward>

request.getRequestDispatcher("other02.jsp").forward(request, response);
```

#### jsp:param

> 在包含某个页面的时候，或者在跳转某个页面的时候，加入这个参数。

``` 
<jsp:forward page="other.jsp">
	<jsp:param value="beijing" name="address"/>
</jsp:forward>

<%= request.getParameter("address")%>
```

### JSP内置对象

> 所谓内置对象，就是我们可以直接在jsp页面中使用这些对象。 不用创建。
> 
> 作用域范围大小：
	pageContext -- request --- session -- application

#### 作用域对象
> 表示这些对象可以存值，他们的取值范围有限定。  setAttribute   和  getAttribute

- pageContext	【PageContext】

> 作用域仅限于当前的页面。  
> 还可以获取到其他八个内置对象。

- request【HttpServletRequest】

> 作用域仅限于一次请求， 只要服务器对该请求做出了响应。 这个域中存的值就没有了。

- session【HttpSession】

> 作用域限于一次会话（多次请求与响应） 当中。 

- application【ServletContext】

> 整个工程都可以访问， 服务器关闭后就不能访问了。 


#### 其它内置对象

- out 【JspWriter】
	- response【HttpServletResponse】
- exception  【Throwable】
- page	 【Object】
- config 【ServletConfig】

### EL表达式

> 是为了简化咱们的jsp代码，具体一点就是为了简化在jsp里面写的那些java代码。

* 写法格式

```
${ 表达式 } 
```

#### 获取4个作用域中存放的值

```
${ pageScope.name }
${ requestScope.name }
${ sessionScope.name }
${ applicationScope.name }

```

#### EL表达式11个内置对象

> 作用域相关

- pageScope
- requestScope
- sessionScope
- applicationScope

> 头信息相关对象
 
- header
- headerValues

> 参数信息相关对象

- param
- params

- cookie
- initparam


### JSTL表达式

> 全称 ： JSP Standard Tag Library  jsp标准标签库

> 简化jsp的代码编写。 替换 <%%> 写法。 一般与EL表达式配合


#### 导入JSTL库

1. 导入jar文件到工程的WebContent/Web-Inf/lib  jstl.jar standard.jar
2. 在jsp页面上，使用taglib 指令，来引入标签库
3. 注意： 如果想支持 EL表达式，那么引入的标签库必须选择1.1的版本，1.0的版本不支持EL表达式。

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

#### 常用标签

```
<c:set></c:set>
<c:if test=""></c:if>
<c:forEach></c:forEach>
```
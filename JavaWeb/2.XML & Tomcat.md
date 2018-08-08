## 二、XML & Tomcat

### 文档声明
- version: 版本号
- encoding: 编码方式
- standalone: no-该文档依赖其它文档 yes-独立的文档
``` <?xml version="1.0" encoding="gbk" standalone="no"?> ```


### CDATA区非法字符

``` <des><![CDATA[<a href=“baidu.com”>百度</a>]]></des> ```

### 解析方式(DOM & SAX)
- jaxp
- jdom
- dom4j (使用广泛)

### schema标签
- xmlns: xml命名空间
- targetNamespace: 目标名称控件
- elementFormDefault: 元素的格式化情况
- element: 元素

### Tomcat文件目录
- bin：包含一些jar, bat文件，启动和终止tomcat
- conf：tomcat的配置，server.xml，webxml
- lib：tomcat运行所需的jar
- logs：日志
- temp：临时文件
- webapps：发布到tomcat服务器上的项目
- work：jsp翻译成java文件
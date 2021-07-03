## 一、AJAX



### 1. AJAX简介

1. AJAX 全称为 Asynchronous JavaScript And XML，就是异步的 JS 和 XML。
2. 通过 AJAX 可以在浏览器中向服务器发送异步请求，最大的优势：无刷新获取数据。
3. AJAX 不是新的编程语言，而是一种将现有的标准组合在一起使用的新方式。



### 2. XML 简介

* XML 可扩展标记语言。
* XML 被设计用来传输和存储数据。
* XML 和 HTML 类似，不同的是 HTML 中都是预定义标签，而 XML 中没有预定义标签。

```xml
//比如说我有一个学生数据：
 name = "孙悟空" ; age = 18 ; gender = "男" ;

//用 XML 表示：
<student>
	<name>孙悟空</name>
	<age>18</age>
	<gender>男</gender>
</student>

//用 JSON 表示：
{"name":"孙悟空","age":18,"gender":"男"}
```



### 3. AJAX 的特点

#### 优点

* 可以无需刷新页面而与服务器端进行通信。
* 允许你根据用户事件来更新部分页面内容。

#### 缺点

* 没有浏览历史，不能回退；
* 存在跨域问题(同源；
* SEO 不友好；



### 4. AJAX 的使用

#### 核心对象

**XMLHttpRequest**，AJAX 的所有操作都是通过该对象进行的。
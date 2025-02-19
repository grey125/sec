### CSRF的利用及原理

#### CSRF简介
	CSRF（Cross-site request forgery）跨站请求伪造，也被称为“One Click Attack”或者Session Riding，通常缩写为CSRF，是一种对网站的恶意 利用。尽管听起来像跨站脚本（XSS），但它与XSS非常不同，XSS利 用站点内的信任用户，而CSRF则通过伪装来自受信任用户的请求来利 用受信任的网站。

#### CSRF攻击原理
	通常情况下web站点中用户的操作是需要登录以后才能进行，而登录用户会在浏览器中存储cookie以此来验证是否登录及登录的用户名是什么。而CSRF跨站请求伪造的攻击原理就是通过在非用户本意的情况下让其进行操作，通俗的说就是伪造一个与用户认证过的站点页面欺骗其进行访问造成恶意操作，例如:a要给b转账100美金，这时hacker伪造了一个站点页面并通过欺骗诱惑其进行访问，最终造成a给hacker转账了10000美金。

### CSRF攻击技巧

#### 伪造表单

```html
<title>在线看片 - 免费</title>
<form action="url" method="post">
<input type="hidden" name="user" value="hacker"/>
<input type="hidden" name="money" value="10000"/>
<input type="submit" name="sub" value="点击进入" />
</form>
```

#### 防御方法

```php
#提交页面进行设置 Referer

$referer = $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
$_SERVER['Referer'] = $referer;

#处理页面进行验证 Referer

if($_SERVER['HTTP_REFERER'] == 'user.php'){
    ...
}
```
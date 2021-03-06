# 关于GFW-Breaker项目的接口说明
## 一.签名算法
#### 在此项目中如不特殊注明,所有接口均使用以下签名算法.
#### 1.过程简述:签名由公钥,私钥,时间戳(精确至秒十位UNIX时间戳)和有效数据进行md5运算得到.
#### 2.以登录接口为例.有效数据为三个参数:id password type:
```
{
    'id':'用户名',
    'password':'密码',
    'type': 'login',
    'app_id':1,
    'timestamp':1581952284
}
```
#### 将签名按照字典序排序,并将参数和值连接. 得到:
```$xslt
app_id1id用户名password密码timestamp1581952284typelogin
```
#### 将私钥(ssssssssss)拼接在尾部:
```$xslt
app_id1id用户名password密码timestamp1581952284typeloginssssssssss
```
#### 进行md5运算:
```$xslt
87e1b97dde88324066aa3b61982bf2a6
```
#### 转换为大写得到签名:
```$xslt
87E1B97DDE88324066AA3B61982BF2A6
```
#### 插入到原有数据中:
```$xslt
//json格式
{
    'id':'用户名',
    'password':'密码',
    'type': 'login',
    'app_id':1,
    'timestamp':1581952284,
    'sign': '87E1B97DDE88324066AA3B61982BF2A6'
}

//txt格式 (这里使用了另外一组数据,如果使用txt格式请注意url编码有效数据)
id=f1991455223%40sina.com&password=123456&sign=0429D9841CB74167F6B259AE38524FEC&type=login&app_id=1&timestamp=1583238631
```
## 二.返回值
|code|msg|data|注释|
|:---:|:---:|:---|:---|
|100|请求成功|返回不固定的数据,请查看接口具体说明|null|
|101|请求成功，即将跳转|跳转url|应跳转到data中传回的url|
|201|不错的尝试|null|签名计算错误|
|202|数据库错误|null|服务端的数据库出错|
|203|参数不完整|null|可能是签名用的参数也可能是有效数据缺少|
|204|状态不正确|要显示的消息|多数为用户被封禁或者未验证邮箱,直接显示data的值即可|
|205|请求次数达到上限|达到上限的资源名词,如email,sms|null|
|206|签名失效|null|签名已经失效|
|207|认证未通过|null|一般为密码错误|
|209|短信服务发生错误|null|null|
|210|验证码错误|null|null|
|211|重复的值|重复的参数名|null|
|212|数据内容格式错误|null|null|
|213|请求速度过快|null|null|
|214|登入点错误|null|null|
|215|参数格式错误|null|通常是多余或不足的参数导致的|
|216|结果不存在|null|用于某些预期必须有结果的接口|
|300|预留调试代码|null|null|
# 三.接口
## 1.登录
|条目|注解|
|---|---|
|请求方式|POST|
|返回值格式|application/json|
|地址|http:///API/identify.php|

|参数|用法|
|---|---|
|type|必须为字符串'login'|
|id|用户输入的邮箱或用户名,后端会自行判断类型|
|password|用户输入的密码|
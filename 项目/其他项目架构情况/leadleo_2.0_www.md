- bin
- crawl
- public
- routes
- views
- app.js

## 登录：

###  前端：

#### 密码登录：

- 1 点击登录

- 2 前端验证

- 3 成功 => 防水

```js
腾讯防水墙简介：
1 在Head的标签内最后加入以下代码引入验证JS文件（建议直接在html中引入）
<script src="https://ssl.captcha.qq.com/TCaptcha.js"></script>

2 在你想要激活验证码的DOM元素（eg. button、div、span）内加入以下id及属性
<!--点击此元素会自动激活验证码-->
<!--id : 元素的id(必须)-->
<!--data-appid : AppID(必须)-->
<!--data-cbfn : 回调函数名(必须)-->
<!--data-biz-state : 业务自定义透传参数(可选)-->
<button id="TencentCaptcha" data-appid="appId" data-cbfn="callback"
type="button">验证</button>

3 为验证码创建回调函数，注意函数名要与data-cbfn相同
window.callback = function(res){
    console.log(res)
    // res（用户主动关闭验证码）= {ret: 2, ticket: null}
    // res（验证成功） = {ret: 0, ticket: "String", randstr: "String"}
    if(res.ret === 0){
        alert(res.ticket)   // 票据
    }
}


4 生成一个验证码对象 手动初始化并绑定到一个元素
const captcha_login_password = new   TencentCaptcha(document.getElementById('login_password'))
captcha_login_password.show()
disable = true

5 前端验证成功后，验证码会调用业务传入的回调函数，并在第一个参数中传入回调结果。
在回调函数发送ajax请求，req,res
sendPassWordLogin:
ajax(/user/loginWithPassword)
{
ticket,
randstr,
id,
countryCode,
password
}

6 setItem('token', r)
```

#### 验证码登录：

- 1  点击发送验证码
```
1 倒计时
2 防水验证
const captcha_code = new TencentCaptcha(document.getElementById('TencentCaptcha'))
captcha_code.show()
3 发送请求:sendSMS 
ajax(/user/loginSendCode)
{
ticket,
randstr,
id:
country:
}
```

- 2  点击登录
```
1 前台验证
2 发送请求：
ajax(/user/loginVerifyCode)
{
ticket,
code
}
3 setItem('token', r)
```

·



### 后端

1 获取页面 get(/login)

2 密码登录：get('/loginWithPassword)

```
1 获取前端传的数据
{ticket,randstr,id,countryCode,password} = req.query
2 机器人验证 require('../utils/007qq') verify(){}
3 密码登录 Customer.loginWithPassword require('../bll/crm') 在数据库找
```

3 发送验证码 get(/user/loginSendCode)

```
1 获取前端传的数据
{ticket,randstr,countryCode} = req.query
2 验证格式
3 根据手机号码，判断客户是否存在 Customer.isExist require('../bll/crm') 在数据库找
4 机器人验证 qq007.verify require('../utils/007qq')
5 redis.getClient()  require('../utils/redis')
```



4 .验证码登录 get('/loginVerifyCode',）

```
1 获取前端传的数据
{ticket,randstr,countryCode} = req.query
2 redis.getContent
3 jwt
```



5  防水 get('/sendSMS)

```
1 获取前端传的数据{ticket,randstr,countryCode,poneNumbers} = req.query
2 对数据进行验证
3 机器人验证 require('../utils/007qq') verify(){}
4 发送手机短信 sendSMS（countryCode, phone, code）{}
 sms require('../utils/ali-sms')  阿里云短信发送
5 res.send() new Result(true) 
```





## 注册：

### 前端：

#### 手机号注册：

1 input 绑定 onchange PhoneChange  验证手机号是否重复

```
PhoneChange（）{
1 验证格式
2 发送ajax ajax('/user/checkMobile）
3 手机号存在，返回xxx，否则，返回success，图片
}
```



#### 邮箱注册：

```
EmailChange(){
1 验证格式
2 发送ajax （'/user/checkEmail'
}
```



1 点击注册

```
1 防水 sendRegister
2 验证成功：post： ajax('/user/register'）token
```

### 后端：

1 获取页面router.get('/register')

2 检测注册手机号：router.get('/checkMobile'）

```
Customer.checkPhone(null, countryCode, mobile) require('../bll/crm')
this.find(params).exec((err, doc) 
res.send(r)

```

3 检测注册邮件 router.get('/checkEmail',）

```
Customer.checkEmail(null, email) require('../bll/crm')
this.find(params).exec((err, doc) 
res.send(r)
```

4 新用户注册

```
1 新建用户信息 Customer.addNew(req.body) require('../bll/crm') 1 验证是否重复 2md5 
3 let _c = new Customer(customer) await _c.save()
2 token jwt
```



## 个人资料修改：

### 前端：

1 发送验证码

2 点击保存

```
1 表单验证
2 put  ajax('/user/modify')
{name,company,company,title,email,mobile,countryCode,gender,verifiedEmail,verifiedMobile}
```



### 后端：

1 个人资料修改：router.put('/modify'）

```
1 let oid = require('mongoose').Types.ObjectId
2 delete req.body._id
3 let customer = await Customer.modify(customerId, req.body)
4 Customer.modify = function (customerId, body)
  delete body._id
  delete body.password
  this.findById(customerId).exec(async (err, doc)
  
  doc.save((err, _doc) => {
   resolve(_doc)
  
```






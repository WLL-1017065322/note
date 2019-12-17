<!-- 作者：徐斌
日期：2019-12-09 -->

# 个人中心vip

## 个人中心vip页面-同步

1、前端

```
操作：用户进入个人中心的vip页面
请求：get(/user/VIP)
```

2、后端

```
请求方法：get
router：/user/VIP
model： AbstractCard，card
bll
    2.1 需要进行用户身份认证
        2.1.1 如果已登录，根据用户id查询card是否存在-判断用户当前身份（用户，会员，过期会员）
            2.1.1.1 如果不存在，说明用户没有购买会员，设置type=“user”(type用于不同会员的前端页面渲染）
            2.1.1.2 如果存在，取card获取用户姓名，用户id，激活日期activeDate，强制失效日期expireAt卡片种类cardType，判断card中卡片种类cardType
                2.1.1.2.1 获取激活日期activeDate+可用时长lifeEnd与当前时间比较
                    2.1.1.2.1.1 如果大于当前日期，说明会员有效，设置type=“vip”
                    2.1.1.2.1.2 如果小于当前日期，说明会员过期，设置type=“outOfTime”
                2.1.1.2.2 获取AbstractCard卡片，判断inShoppingList为true的显示
                    获取卡片的等级level、cardType、image、originalPrice、price
            render页面
        2.1.2 如果没有登录，提示用户未登录，重定向注册登录页面
返回值：如果请求成功，返回对象
        {
            userObj{
                userId: String，
                userName： string，
                type: （用户，会员，过期会员）
                activeDate：date，用户激活vip的日期
                lifeEnd： number，可用时长
            }
            cardObj{
                cardType: 卡片种类
                level: 卡片等级，0-9；>9
                image
                originalPrice：卡片原始价
                price： 卡片当前价格
            }
        }
        如果失败，返回对象{ msg：错误信息}
```

## 个人中心vip页面，发起支付请求-异步

1、前端：

```
操作：用户点击选择相应的卡片|报告|付费加速，或者改变支付方式后，会向后端提供一个商品信息，同时页面付款码处loading
路由：user/purchaseVipByWechat
方法：post
参数：{
        message: 购买的商品信息（卡片种类，报告标题，加速报告标题）
        Amount：商品金额，单位是分，不可有小数
        pay：支付方式wechat|alipay
}
返回：请求成功，停止loading，返回二维码
    请求失败，停止loading，返回失败原因
```

2、后端

```
请求方法：post
router：user/purchaseVipByWechat
model：AbstractCard，report
bll：
    2.1 判断用户的支付方式pay
        2.1.1 如果用户选择微信支付
            官网微信支付请求接口 https://api.mch.weixin.qq.com/pay/unifiedorder
            详情见文档
        2.1.2 如果用户选择支付宝支付
            官网支付宝支付请求接口 https://openapi.alipay.com/gateway.do
            详情见文档
        拿到微信或支付宝生成的二维码链接，渲染到前端页面
```

## 实时检测订单状态

websocket实现服务器端主动向前端推送消息

1、前端

```
if('webSocket' in window){
    var wsObj= new WebSocket(url);建立连接
}
wsObj.onmessage = function(event){
    if(event.data){
        收到消息，判断消息返回
        如果正在处理请求
            页面付款码处loading
        如果返回成功
            停止付款码loading
                vip页面-给与用户消息提示，重新加载页面，改变用户持有卡片数据
                报告购买-给用户消息提示，用户拥有单次有限卡，返回报告页面
                加速购买-用户消息提示，用户拥有加速权限，返回首页，加速数据渲染
    }
}
wsObj.onerror = function(){
    //异常的时候触发
    撤销订单接口-微信、支付宝
}
wsObj.onclose = function(){
    //连接断开时触发，关闭页面时
    撤销订单接口-微信、支付宝
}
```

2、后端

后端引入ws模块，构建服务器，监听对应事件

```
var ws = require("ws"); // 加载ws模块;
var wsServer = new ws.Server({
    url-微信、支付宝
});
// 建立连接，监听客户端请求，绑定对应事件;
function eventListener (wsObj) {
    addListener(wsObj);
}
wsServer.on("connection", eventListener);
// 各事件处理逻辑
function addListener(wsObj) {
    wsObj.on("message", function(data) {
        setTimeout(()=>{
            wsObj.send({code: 00, msg: 'ing'});  -接收到请求，正在处理
        },1000);
        setTimeout(()=>{
            wsObj.send({code: 01, msg: 'end'});   -完成请求，回复前端
            wsObj.close()
        },3000);
    });
    wsObj.on("error", function(err) {
        wsObj.send({code: 1, msg: 'err'})  -操作异常
    });
}
```

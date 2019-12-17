## Node.js+Express 开发之Cookie、Session 使用详解

原创： 雨林星空 [全栈开发者中心](javascript:void(0);) *3天前*

　　**为什么有cookie 和 session ？**

　　因为HTTP协议是没有状态的，当用户再次访问网站时，没法判断之前是否登陆过，于是就有了cookies和session，用来保存用户的一些信息。



　　**cookie 和 session 区别？**

　　cookie 是存放在客户端浏览器的，每个域名下通常限制为50个cookie，每个cookie 的值大小限制为4K。session 是存放在服务器端的，可以存储无限大的数据，但大量的session，会占用较多的服务器内存。

　　cookie 只能存放字符串类型数据，session可以存放任意类型数据。

　　cookie 是保存在浏览器客户端的，所以也很容易被提取，安全方面存在隐患。session 保存在服务器端相对安全。



　　**cookie的工作原理**

　　当用户登录网站成功，服务器会在返回响应数据的同时，将用户的cookie信息也下发到客户端，之后客户端每次发起请求会携带着这个cookie，从而免去网站登录的步骤。



　　**session的工作原理**

　　用户的session数据保存在服务器内存中，服务器只下发一个session标识 (session_id)，它是一个唯一的随机字串，被保存到cookie中，下次浏览器的请求携带着包含session_id的cookie，然后服务器在内存中查询该session_id 是否有对应的数据，如果有则是已登录用户。

　　看起来 session 技术相当于Cookie技术的升级，session是基于cookie的，但是cookie只是起到了 session_id 载体的作用，而url的查询参数，http请求头里的字段都可以起到session_id 载体的作用，所以没有cookie也可以实现session。另外，用户的session数据不仅可以存放在服务器内存中，也可以存放到文件或数据库中，要持久化session 同时也要持久化cookie的session_id。



　　**nodejs+express 开发中 cookie 的使用**

　　node.js 的web框架 express 在4.x版本后，许多模块不再包含在其中，而是需要单独下载安装。

　　cookie 常用模块是 cookie-parser，安装命令如下：

- 

```
npm install cookie-parser
```

　　**基本用法**

　　代码示例：

```
let express = require("express");
let cookieParser = require("cookie-parser"); // 引用cookie-parser

let app = express();
app.use(cookieParser()); // 使用cookie中间件cookie-parser

// 首页，读取cookie
app.get("/", function (req, res) {
    console.log("name: " + req.cookies.name, "profile: " + JSON.stringify(req.cookies.profile));
    res.send("name: " + req.cookies.name + "<br/>" + "profile: " + JSON.stringify(req.cookies.profile));
});

// 登录，注册cookie
app.get("/login", function (req, res) {

    // 定义cookie参数对象
    let cookieOptions = {
        httpOnly: true,
        maxAge: 10 * 60 * 1000, // 10分钟后cookie失效
    }

    // 创建cookie，值为字符串
    res.cookie("name", "xiaoyu", cookieOptions);

    // 创建cookie，值可以写成json对象形式 {....} 、[...]
    res.cookie('profile', {
        age: '25',
        height: '160cm',
        weight: '50kg'
    }, cookieOptions);

    res.send("已注册cookie");
});

// 退出，清除cookie
app.get("/exit", function (req, res) {
    res.clearCookie("name");
    res.clearCookie("profile");
    res.send("已清除cookie");
});

app.listen(3000);
```



　　**使用签名cookie**

　　使用签名 cookie，起防篡改功能。

　　代码示例：

```
let express = require("express");
let cookieParser = require("cookie-parser"); // 引用cookie-parser

let app = express();
app.use(cookieParser("1234567abcdefg")); // 使用cookie-parser，传入签名防篡改，可自定义一个复杂的字串

// 首页，读取cookie
app.get("/", function (req, res) {
    console.log("name: " + req.signedCookies.name, "profile: " + JSON.stringify(req.signedCookies.profile));
    res.send("name: " + req.signedCookies.name + "<br/>" + "profile: " + JSON.stringify(req.signedCookies.profile));
});

// 登录，注册cookie
app.get("/login", function (req, res) {

    // 定义cookie参数对象
    let cookieOptions = {
        httpOnly: true,
        maxAge: 10 * 60 * 1000, // 10分钟后cookie失效
        signed: true // 使用签名
    }

    // 创建cookie，值为字符串
    res.cookie("name", "xiaoyu", cookieOptions);

    // 创建cookie，值可以写成json对象形式 {....} 、[...]
    res.cookie('profile', {
        age: '25',
        height: '160cm',
        weight: '50kg'
    }, cookieOptions);

    res.send("已注册cookie");
});

// 退出，清除cookie
app.get("/exit", function (req, res) {
    res.clearCookie("name");
    res.clearCookie("profile");
    res.send("已清除cookie");
});

app.listen(3000);
```

 

　　**cookie 参数选项说明：**

 　　**domain**：表示cookie在什么域名下有效，默认为当前网站域名。如果跨域访问，A站域名为 A.test.com，B站域名为 B.test.com，那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为: ".test.com"

　　**expires**：cookie过期时间，类型为Date。例如：expires: new Date(Date.now() + 10*60*1000)  表示10分钟后cookie过期。如果没有设置或者设置为0，那么该cookie 在关闭浏览器后失效。expires早在http/1.0中已经定义，max-age在http/1.1中定义， 现在基本都是http/1.1，expires已经被max-age属性所取代，建议优先使用maxAge

　　**maxAge**：和expires类似，设置cookie过期的时间，指明从现在开始，多少毫秒以后cookie到期。maxAge:10*60*1000  表示10分钟后Cookie过期。 完全不写 maxAge 项，只要关闭浏览器cookie就失效。maxAge:0  表示从客户端删除此cookie。  

　　**httpOnly**：禁止JS脚本document.cookie 读取cookie内容，防止XSS攻击产生. 默认为false。建议设置为true

　　**path**：cookie在什么路径下有效，默认为'/'

　　**secure**：当secure值为true时，cookie在HTTP中是无效，只在HTTPS中才有效。默认为false

　　**signed**：使用签名，起到防篡改功能。默认为false。  此项并不是加密，cookie值仍然被明文显示出来，对于cookie的加密可自己使用md5、sha1、hmac、aes等方法，或使用中间件cookie-encrypter
 

　　**对 cookie 加密**

　　这里使用第三方模块 cookie-encrypter 对cookie 进行加密。该模块默认采用的是AES256 对称加密算法

​     安装cookie-encrypter

- 

```
npm install cookie-encrypter
```

　　代码示例

```
let express = require("express");
let cookieParser = require("cookie-parser");
let cookieEncrypter = require("cookie-encrypter"); // 引用cookie-encrypter 模块

let app = express();
const secretKey = 'foobarbaz1234567foobarbaz1234567'; // 默认cookie-encrypter模块使用的是 AES256 加密算法，须输入32个字符
app.use(cookieParser(secretKey));
app.use(cookieEncrypter(secretKey)); // 传入密钥，加密cookie，主要是这句

app.get("/", function (req, res) {
    console.log("name: " + req.signedCookies.name, "profile: " + JSON.stringify(req.signedCookies.profile));
    res.send("name: " + req.signedCookies.name + "<br/>" + "profile: " + JSON.stringify(req.signedCookies.profile));
});

app.get("/login", function (req, res) {
    let cookieOptions = {
        httpOnly: true,
        signed: true,
        maxAge: 10 * 60 * 1000,
        // plain: true  //  该选项是cookie-encrypter模块的选项。等于true时，表示不再使用cookie-encrypter进行加密。
    };
    res.cookie('name', 'xiaoyu', cookieOptions);
    res.cookie('profile', {
        age: '25',
        height: '160cm',
        weight: '50kg'
    }, cookieOptions);
    res.send("已注册cookie");
});

app.get("/exit", function (req, res) {
    res.clearCookie('name');
    res.clearCookie('profile');
    res.send("已清除cookie");
});

app.listen(3000);
```



　　**nodejs+express 开发中 session 的使用**

　　处理session常用的中间件之一是 express-session

　　安装 express-session 模块，命令如下：

- 

```
npm install express-session
```

　　**基本用法**

　　代码示例

```
let express = require("express");
let session = require("express-session");
let app = express();

app.use(session({
    secret: "nodejs world",
    resave: false,
    saveUninitialized: false,
    name: "session_id",
    rolling: true,
    cookie: {
        path: '/',
        httpOnly: true,
        secure: false,
        maxAge: 30 * 60 * 1000 // 设置session_id的cookie有效时间，也是后台session的有效时长。（默认为值为null，当关闭浏览器时，session_id cookie失效）。
    }
}));

app.get("/", function (req, res) {
    if (req.session.login) {
        res.send("欢迎您" + req.session.userName + ", " + req.sessionID);
    } else {
        res.send("你没有登录！");
    }
});

app.get("/login", function (req, res) {
    req.session.login = true;
    req.session.userName = "xiaoyu";
    res.send("您已经成功登录");
});

app.get("/exit", function (req, res) {
    req.session.destroy();
    res.send("您已经退出");
});

app.listen(3000);
```



　　**session 配置选项说明**

　　**secret****:** 对session_id cookie的签名密钥。

　　**resave**: 强制保存 session 即使它并没有变化。默认为true，建议设置成false

　　**saveUninitialized**: 强制将未初始化的session存储。默认为 true。设置为true 不管用不用到session都会初始化。设置为false 用到session时才会去初始化。

　　**name**: 这里设置session_id 的cookie的名字。默认名为 'connect.sid'

　　**rolling**: 在每次请求时强行设置cookie，这将重置session-id 的 cookie过期时间。默认：false

　　**cookie**:  session_id 的cookie 选项。缺省值是 { path: '/', httpOnly: true, secure: false, maxAge: null }

 

　　**将session 存放在 mongoDB 数据库中**

　　session 默认存放在内存中，也可以存放到 redis，mongodb 等数据库中。express 生态中都有相应模块支持。

　　将session存储到mongodb 数据库，可以安装connect-mongo模块支持

- 

```
npm install connect-mongo
```

　　代码示例

```
let express = require("express");
let session = require("express-session");
let MongoStore = require('connect-mongo')(session);  // 引用 connect-mongo

let app = express();

app.use(session({
    secret: "nodejs world",
    resave: false,
    saveUninitialized: false,
    name: "session_id",
    rolling: true,
    cookie: {
        path: '/',
        httpOnly: true,
        secure: false,
        maxAge: 30 * 60 * 1000  // session_id cookie 和 session的有效时长
    },
    store: new MongoStore({
　　　　　url: 'mongodb://localhost:27017/mydb', // 指定mongoDB 主机地址、端口、数据库
　　　　　ttl: 30*60 // 设置session过期时间，时间到自动移除。单位秒。一般设置为30分钟即可。未设置cookie:maxAge时有效。
    })
}));

app.get("/", function (req, res) {
    if (req.session.login) {
        res.send("欢迎您" + req.session.userName + ", " + req.sessionID);
    } else {
        res.send("你没有登录！");
    }
});

app.get("/login", function (req, res) {
    req.session.login = true;
    req.session.userName = "xiaoyu";
    res.send("您已经成功登录");
});


app.get("/exit", function (req, res) {
    req.session.destroy();
    res.send("您已经退出");
});

app.listen(3000);
```



　　**将session 存放在 redis 数据库中** 　

　　将session存储到redis 数据库，可以安装redis-mongo模块支持。事先需安装好redis的nodejs客户端

- 
- 

```
npm install redisnpm install connect-redis
```

　　代码示例



```v
let express = require("express");
let session = require("express-session");
let redis = require("redis"); // 引用redis客户端
let RedisStore = require('connect-redis')(session); // 引用connect-redis
let app = express();

let redisClient = redis.createClient({
    // 设置redis 主机地址、端口、数据库、密码
    host: "localhost",
    port: "6379",
    //password: "123456",
    db: 1
});

app.use(session({
    secret: "nodejs world",
    resave: false,
    saveUninitialized: false,
    name: "session_id",
    rolling: true,
    cookie: {
        path: '/',
        httpOnly: true,
        secure: false,
        maxAge: 30 * 60 * 1000 // session_id cookie 和 session的有效时长
    },
    store: new RedisStore({
        client: redisClient, // 指定redis客户端
        ttl: 30 * 60, // 设置session过期时间，时间到自动移除。单位秒。一般设置为30分钟即可。未设置cookie:maxAge时有效。
        logErrors: true, // 是否打印redis出错信息，默认false
        prefix: "sess:" // key前缀，默认为 "sess:"
    })
}));

app.get("/", function (req, res) {
    if (req.session.login) {
        res.send("欢迎您" + req.session.userName + ", " + req.sessionID);
    } else {
        res.send("你没有登录！");
    }
});

app.get("/login", function (req, res) {
    req.session.login = true;
    req.session.userName = "xiaoyu";
    res.send("您已经成功登录");
});

app.get("/exit", function (req, res) {
    req.session.destroy();
    res.send("您已经退出");
});

app.listen(3000);
```
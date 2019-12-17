1 (node:1964) UnhandledPromiseRejectionWarning: Error [ERR_HTTP_HEADERS_SENT]: Cannot set headers after they are sent to the client

原因，异步请求post，使用res.redirect

解决方法：去掉res.redirect()



2 Error [ERR_HTTP_HEADERS_SENT]: Cannot set headers after they are sent to the client

现象：有热词的所有页面无法显示

原因：热词部分async/await有问题

解决：

- 去掉route里的await （不行）
- 切换npm源，不用软链接，把utils，config文件复制到项目里
- 可能是app.js 没连数据库在app.js 引入 require('./conf/db')



3 项目报错：。。。一大串错误，好像是什么什么没有定义：

1 打开 robo 3t

2 script文件夹：sh restore.sh

3 ...
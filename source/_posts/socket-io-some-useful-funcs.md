title: socket.io 中一些有用的方法
date: 2015-08-11 16:08:32
tags: socket.io
---
### io.use
注册中间件。看一下下面的这个例子
```javascript
// 服务端代码
var io = require('socket.io')(); // 引用 socket.io 库并实例化

/** 注册中间件 **/
io.use(function (socket, next) {
  var query = socket.handshake.query; // 获取客户端的连接 url 中的参数
  var password = query.password; // 读取参数中密码

  if (password === '123456') return next(); // 如果密码正确，则执行返回 next()
  next(new Error('密码错误')); // 如果密码错误，则抛出异常
});

/** 监听事件 **/
io.on('connection', function (socket) {
  socket.on('disconnect', function () {
    // 客户端断线
  });

  socket.on('message', function (body) {
    // 服务器收到客户端发来的消息
  });
});
```
```javascript
// 客户端代码
<script src='/socket.io/socket.io.js'></script> // 加载 socket.io.js
<script>
  var socket = io('http://localhost', {query: 'password=654321'}); // 实例化并连接到 socket 服务器，传入密码参数

  /** 监听 error 事件 **/
  socket.on('error', function (err) {
    console.log(err); // 打印 err，注：err 是字符串类型
  });

  socket.send('hello server');
</script>
```
可以看到，当客户端尝试连接服务器时，因为传入的密码是 654321，导致服务端的中间件认证失败，触发客户端的 error 事件。客户端代码第 8 行会打印出“密码错误”这几个字。

注意服务端代码中第 9 行的 return 不能省略。
```javascript
// 服务端代码
var io = require('socket.io')(); // 引用 socket.io 库并实例化

/** 注册中间件 **/
io.use(function (socket, next) {
  var query = socket.handshake.query; // 获取客户端的连接 url 中的参数
  var password = query.password; // 读取参数中密码

  if (password === '123456') next(); // 如果密码正确，则执行返回 next()
  next(new Error('密码错误')); // 如果密码错误，则抛出异常
});
```
注意第 9 行，如果省略 return，则会导致第 10 行代码也被执行。可以将代码修改成下面的更易理解
```javascript
// 服务端代码
var io = require('socket.io')(); // 引用 socket.io 库并实例化

/** 注册中间件 **/
io.use(function (socket, next) {
  var query = socket.handshake.query; // 获取客户端的连接 url 中的参数
  var password = query.password; // 读取参数中密码

  if (password === '123456') {
    next(); // 如果密码正确，则执行返回 next()
  } else {
    next(new Error('密码错误')); // 如果密码错误，则抛出异常
  }
});
```
### ACK 模式
```javascript
// 服务端
var io = require('socket.io')();

io.on('connection', function (socket) {
  socket.on('chat', function (msg, callback) {
    callback({
      success: true
    });
    io.emit('chat', msg);
  });
});
```
```javascript
// 客户端
<script>
  var socket = io();
  socket.on('connect', function () {
    socket.emit('chat', 'Hello', function (response) {
      console.log(response.success); // 打印结果为 true
    });

    socket.on('chat', function (response) {
      console.log(response); // 打印结果为 Hello
    });
  });
</script>
```
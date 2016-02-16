title: nodejs 鼠标点击
date: 2016-02-16 19:53:47
tags:
---

今天玩 Clicker Heroes 的时候点鼠标点的累死,要是 mac 下也有个按键精灵就好了.在网上搜索一番后,找到了 robotjs 这个库,正和我意!

于是,三行鼠标左键连点代码就有了.
```
var robot = require("robotjs");

robot.setMouseDelay(80);

while (true) robot.mouseClick();
```

可以轻轻松松在 Clicker Heroes 中刷刷刷啦.

robotjs 的 wiki 可以查阅: https://github.com/octalmage/robotjs
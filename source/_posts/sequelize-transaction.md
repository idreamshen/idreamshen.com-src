title: sequelize 中的事务循环插入
date: 2015-08-13 16:14:13
tags: sequelize
---
sequelize 的事务模式详细使用方法可以参考官方文档
http://docs.sequelizejs.com/en/latest/docs/transactions

本文主要介绍一下如何在 sequelize 的事务中进行循环插入。
假设有两个模型 Order 和 OrderItem，一条 Order 记录会对应若干条 OrderItem 记录
```javascript
var Promsise = require('bluebird');


function insert () {
  var itemIds = [1,2,3];

  return sequelize.transaction(function (t){
    return Order.create({
      name: 'XXX订单',
      price: 100
    }, {transaction: t}).then(function (order){
      return Promise.map(itemIds, function(itemId) {
        return OrderItem.create({
          order_id: order.get('id'),
          item_id: itemId
        }, {transaction: t});
      });
    });
  });
}

```
解决的方案就是采用 Promise.map 这个方法，它能遍历数组并返回 promise。
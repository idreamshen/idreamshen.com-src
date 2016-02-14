title: 2015年终小结
date: 2016-02-13 12:12:49
tags:
---
2015年是毕业后的第一年,工作经验终于增加了一年.这一年的努力如果要打个分的话,我给自己七分.这一年最大的变化是我能熟练的使用 Nodejs 开发各种项目以及开始学习了 Java.那接下来我就谈谈这一年里我对这两门语言的感受吧.

Nodejs 是我这一年里使用次数最多的语言,我用它完成了若干个的项目.总结下来,用 Nodejs 开发有两个优点,开发快和性能高.我先说说开发快,由于 Nodejs 使用了 Javascript 这门语言作为编程语言, Javascript 的动态特性使得其在编写代码时非常简单.第二个优点是性能高,由于 Javascript 支持回调函数,使得异步IO并行操作,节省了大量的等待时间.但是 Nodejs 开发大型合作项目就有点麻烦了.一, IDE 无法很好地做到代码提示,二, 变量名方法名等重构困难.三, 弱类型很容易埋下了隐藏的漏洞.

我举2个实例来看一下 Nodejs 和 Java 在编程上有什么区别.

1.假设需求:通过一个网页向用户展示某件商品的相关信息.

Nodejs 版 (Express Framework)
```
var express = require('express');
var app = express();
var Promise = require('bluebird');
var model = require('./models/index');

app.get('/item', function(req, res) {
    var itemId = req.query.itemId;
    var myUserId = req.session.userId;

    Promise.all([
        model.user.findById(myUserId),
        model.item.findById(itemId),
        model.itemDetail.findByItemId(itemId)
    ]).spread(function(user, item, itemDetail) {
        res.render('item', {
            me: user,
            item: item,
            itemDetail: itemDetail
        });
    }).catch(function(err) {
        res.render('error'); // 渲染 error 页面
    });
});
```

Java 版 (Spring Boot Framework)
```
@Controller
public class ItemController {
    @Autowired private UserRepository userRepository;
    @Autowired private ItemRepository itemRepository;
    @Autowired private ItemDetailRepository itemDetailRepository;

    @RequestMapping(method = RequestMethod.GET, path = '/item')
    public String itemRender(@RequestParam(value = "itemId", required = true) int itemId,
                            Model model) {

        Session session;
        // ...
        int userId = session.getUserId();

        UserModel userModel = userRepository.findOne(userId);
        ItemModel itemModel = itemRepository.findOne(itemId);
        ItemDetailModel itemDetailModel = itemDetailRepository.findByItemId(itemId);

        model.addAttribute('me', userModel);
        model.addAttribute('item', itemModel);
        model.addAttribute('itemDetail', itemDetail);

        return "shop/item";
    }
}
```

Nodejs 版中第 11 行至 13 行是从数据库获取信息,通过 Promise 的 all 方法包裹后,三个 sql 语句并行执行,假设 3 条 sql 语句耗时分别为 1ms 1.2ms 0.8ms,那么第 14 行的代码将在 1.2ms 后执行.再看 Java 版,第 15 行至 17 行是串行查询,所以第 19 行会在 1 + 1.2 + 0.8 = 3ms 后执行.这也就是一定程度上, Nodejs 的速度要优于 Java.

2.假设需求:实现一个机器人简单逻辑

Nodejs 版
```
function Bot(id) {
    this.id = id;
}

// 说话
Bot.prototype.talk = function(message) {
    // ...
}

// 走路
Bot.prototype.walk = function(direction, step) {
    // ...
}
```

Java 版
```
public class Bot {
    private int id;

    public Bot(int id) {
        this.id = id;
    }

    public void talk(String message) {
        // ...
    }

    public void walk(Direction direction, int step) {
        // ...
    }
}

```

从代码结构来看几乎是差不多的,但是当你运行上百个上千个机器人时,就有点区别了.假设生成了1000个机器人. Nodejs 中每个机器人的不同的属性就是 id,其他方法都是相同的引用,也就是会创建1000 份 Bot 对象和 1 份 Bot 原型链上的方法.而在 Java 中会创建 1000 个 Bot Class.两者的内存占用相差是不小的.这是我的个人理解,可能有错误.

以上两个例子似乎都在夸 Nodejs ,但是在实际使用中, Nodejs 并不是如此好驾驭的,上面我也提到了一些缺点. Nodejs 个人觉得适合小型项目比较好,比如说智能家居上面.对的,今天我花了五百大洋买了个树莓派,希望能把 Nodejs 折腾上去,那么用事件的模式去处理日常生活的各种指令应该是不错的体验(等待快递中 0.0).

展望一下新的一年吧.由于工作上的需要,我可能得把更多的心思花到 Java 上了.为了不落下 Nodejs 的学习,我将尽可能的用 Nodejs 去编写树莓派上的各种应用.同时还要补一下编程的基础知识,编程的设计模式啦之类的.

暂时就写到这吧.

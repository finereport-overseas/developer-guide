# 创建第一个组件

## 准备工作

首先，Fine-Decision 决策平台前端采用帆软内部开源框架 fineui 开发，在进行拓展开发的时候希望你能够对 FineUI 有一个基础的了解：

[http://fanruan.design/doc.html?post=0169cf558d](http://fanruan.design/doc.html?post=0169cf558d)

## 创建第一个组件

首先，我们通过继承超类 `BI.Widget` 定义了一个组件，然后用 `BI.shortcut` 声明一个唯一名字标识它，最后通过 `BI.createWidget` 方法创建一个组件实例。

```js
var Widget = BI.inherit(BI.Widget, {
    props: {
        baseCls: "my-first-widget"
    },

    render: function () {
        return {
            type: "bi.label",
            text: "this is my first widget"
        };
    },

    sayHello: function () {
        console.log("hello");
    }
});

BI.shortcut("my.first_widget", Widget);

BI.createWidget({
    type: "my.first_widget",
    element: "#app"
});
```

如何获取一个组件的引用呢？有两种方法，一种是 `BI.createWidget` 方法会返回组件的引用，另一种是通过 `ref` 的形式获取，和 React 很相似：

```js
var Widget = BI.inherit(BI.Widget, {
    props: {
        baseCls: "my-second-widget"
    },

    render: function () {
        var self = this;
        this.widget1 = BI.createWidget({
            type: "my.first_widget"
        });

        var jsonObj = {
            type: "my.first_widget",
            ref: function (_ref) {
                self.widget2 = _ref;
            }
        };

        return {
            type: "bi.vertical",
            items: [
                this.widget1,
                jsonObj
            ]
        };
    }
});

BI.shortcut("my.second_widget", Widget);

var secondWidget = BI.createWidget({
    type: "my.second_widget",
    element: "#app"
});

secondWidget.widget1.sayHello();
secondWidget.widget2.sayHello();
```

# Create Your First Component

## Prerequisites

First, the Fine-Decision platform frontend is built using FanRuan's internal open-source framework FineUI. Before doing extension development, it is recommended that you have a basic understanding of FineUI:

[http://fanruan.design/doc.html?post=0169cf558d](http://fanruan.design/doc.html?post=0169cf558d)

## Create Your First Component

First, we define a component by extending the base class `BI.Widget`, then register it with a unique name using `BI.shortcut`, and finally create a component instance using `BI.createWidget`.

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

How do you get a reference to a component? There are two ways: `BI.createWidget` returns the component reference, or you can use the `ref` pattern, similar to React:

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

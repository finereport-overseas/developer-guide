# 设定主题

平台主题框架的设定包括布局策略配置和布局信息配置。

平台内置了两种模式：classic 和 modern 模式，可以基于这两种模式初始化进行定制开发。开发者可以根据主题定制的程度自由选择，推荐基于内置模式进行拓展。如果对 FineUI 有深度了解，也可以自定义模式。

## 布局策略

布局策略用于配置平台的布局方式。平台默认的布局方式是绝对布局实现的「上、左、右」模式，划分为 header、menu 栏和 body 主内容区：

```js
type: "bi.absolute",
items: [
    {
        el: {
            type: "bi.absolute",
            items: [
                {
                    el: {
                        type: "dec.body"
                    },
                    top: 0, bottom: 0, right: 0,
                    left: layoutConfig.west.invisible ? 0 : layoutConfig.west.width
                }, {
                    el: {
                        type: "dec.menu",
                        width: layoutConfig.west.width, // 宽度 默认70
                        invisible: layoutConfig.west.invisible
                    },
                    top: 0, bottom: 0,
                    left: 0
                }
            ]
        },
        top: layoutConfig.north.invisible ? 0 : layoutConfig.north.height, left: 0, right: 0, bottom: 0
    }, {
        el: {
            type: "dec.header",
            height: layoutConfig.north.height,
            invisible: layoutConfig.north.invisible
        },
        height: layoutConfig.north.height,
        top: 0, left: 0, right: 0
    }
]
```

配置布局策略的方式：

```js
BI.config("dec.provider.theme", function (provider) {
    provider.inject({
        layoutStrategy: {
            //  自定义的布局策略
        }
    });
});
```

## 布局信息

布局信息用于配置内置布局策略的部分属性（宽高等）。完整配置对象如下：

```js
{
    // 框架布局配置属性
    config4Frame: {
        north: {  // 顶部
            height: 40,
            invisible: false
        },
        west: {  // menu
            width: 70,
            invisible: false
        },
        center: {
            left: {  // 主内容区左侧面板
                width: 240,
                maxSize: 500,
                minSize: 240
            }
        }
    }
}
```

配置示例：加宽目录面板，提高最大宽度

```js
BI.config("dec.provider.layout", function (provider) {
    provider.inject({
        layoutConfig: {
            center: {
                left: {
                    width: 340,
                    maxSize: 800
                }
            }
        }
    });
});
```

## 自定义基础模式

可以设置 `base` 属性指定基础模式。如果不想使用内置模式、完全自定义，可以将 `base` 字段设为空，之后指定 provider 的 key 值：

```js
BI.config("dec.provider.theme", function (provider) {
    provider.inject({
        base: "",
        provider: "my.own.provider"
    });
});

var Provider = function () {

    this.$get = function () {
        return BI.inherit(BI.OB, {

            initConfig: function (config) {
                // 初始化自定义设置，这里示例为经典主题模式下目录节点的配置
                BI.config("dec.constant.menu.items", function (items) {
                    BI.some(items, function (index, item) {  //  some方法类似于lodash some方法
                        if (item.value === "directory") {
                            item.cardType = "dec.frame.classic";
                            return true;
                        }
                    });
                    return items;
                });
            }
        });
    };
};
BI.provider("my.own.provider", Provider);
```

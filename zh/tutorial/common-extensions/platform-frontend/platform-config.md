# 决策平台配置

## BI.config

通过 `BI.config` 方法配置平台提供的拓展点，决策平台提供了一系列 provider 来配置拓展，调用方式如下：

```js
BI.config("dec.provider.xxx", function (provider) {
    //  调用provider提供的方法注册拓展
    provider.xxx()
});
```

示例：拓展管理系统节点

```js
// 管理系统
BI.config("dec.provider.management", function (provider) {
    provider.inject({
        modules: [
            {
                value: "custom_manage",
                id: "decision-management-custom-manage",
                text: "自定义管理",
                cardType: "my.custom_manage",
                cls: "setting-font",
                dev: true
            }
        ]
    });
});
```

`BI.config` 也可以用来配置组件，修改组件的 options 信息。

示例：将经典模式下侧边栏目录树组件替换成自定义的组件

```js
BI.config("dec.frame.classic.aside.entry_tree", function (options) {
    options.type = "your.onw.tre"; // 将组件的type替换为自定义的组件
    return options;
});
```

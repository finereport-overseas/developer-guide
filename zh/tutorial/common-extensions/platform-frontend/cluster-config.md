# 新增集群配置

## 新增缓存系统

**key**

`dec.constant.intelligence.cluster.state.server`

**示例**

```js
// 新增缓存系统的配置
BI.config("dec.constant.intelligence.cluster.state.server", function (items) {
    items.unshift({
        value: "ehcache",
        text: BI.i18nText("内置Ehcache"),
        cardType: "dec.plugin.intelligence.cluster.state.ehcache"
    });
    return items;
});
```

页面实现：

```js
// 页面实现
!(function () {
    var Plugin = BI.inherit(BI.Widget, {

        props: {
            baseCls: ""
        },

        render: function () {
            return {
                type: "bi.label",
                text: "新增的缓存系统"
            };
        },

        /**
         * 校验函数，可选
         * 只有返回值为 true 的情况下才会测试连接保存
         * @returns {boolean}
         */
        validation: function () {
            return true;
        },

        /**
         * 取值函数，必选
         * 返回的值会与 {open: Boolean, system: String} 合并
         * @returns {{}}
         */
        getValue: function () {
            return {};
        }
    });
    BI.shortcut("dec.schedule.task.plugin", Plugin);
})();
```

## 新增文件服务器

**key**

`dec.constant.intelligence.cluster.file.server`

**示例**

```js
// 新增文件服务器的配置
BI.config("dec.constant.intelligence.cluster.file.server", function (items) {
    items.push({
        value: "xx",
        text: "XX文件协议",
        cardType: "dec.plugin.intelligence.cluster.file.xx"
    });
    return items;
});
```

页面实现和上面的状态服务器类似，区别在于插件定义的页面不是整体，而是其中的一部分，并且返回的 value 值是传递到名为 `config` 的属性当中。

![文件服务器配置页面示例](https://kms.finedevelop.com/download/attachments/75970377/image2019-8-20_16-59-10.png?version=1&modificationDate=1566291561000&api=v2)

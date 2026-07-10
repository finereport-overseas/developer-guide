# Add Cluster Configuration

## Add a Cache System

**Key**

`dec.constant.intelligence.cluster.state.server`

**Example**

```js
// Add configuration for a cache system
BI.config("dec.constant.intelligence.cluster.state.server", function (items) {
    items.unshift({
        value: "ehcache",
        text: BI.i18nText("Built-in Ehcache"),
        cardType: "dec.plugin.intelligence.cluster.state.ehcache"
    });
    return items;
});
```

Page implementation:

```js
// Page implementation
!(function () {
    var Plugin = BI.inherit(BI.Widget, {

        props: {
            baseCls: ""
        },

        render: function () {
            return {
                type: "bi.label",
                text: "New Cache System"
            };
        },

        /**
         * Validation function, optional.
         * The connection test and save are only performed when this returns true.
         * @returns {boolean}
         */
        validation: function () {
            return true;
        },

        /**
         * Value getter, required.
         * The returned value will be merged with {open: Boolean, system: String}.
         * @returns {{}}
         */
        getValue: function () {
            return {};
        }
    });
    BI.shortcut("dec.schedule.task.plugin", Plugin);
})();
```

## Add a File Server

**Key**

`dec.constant.intelligence.cluster.file.server`

**Example**

```js
// Add configuration for a file server
BI.config("dec.constant.intelligence.cluster.file.server", function (items) {
    items.push({
        value: "xx",
        text: "XX File Protocol",
        cardType: "dec.plugin.intelligence.cluster.file.xx"
    });
    return items;
});
```

The page implementation is similar to the state server above. The difference is that the plugin-defined page is not the full page but only a portion of it, and the returned `value` is passed into a property named `config`.

![File server configuration page example](https://kms.finedevelop.com/download/attachments/75970377/image2019-8-20_16-59-10.png?version=1&modificationDate=1566291561000&api=v2)

# 新数据连接数据库插件接口

## 接口作用

新数据连接管理页面支持以插件的方式增加数据连接，方便于在内置数据连接不足的情况下拓展其他数据连接。

## Provider

`dec.connection.provider.datebase`

## 示例

```js
// 注册Redis插件
BI.config('dec.connection.provider.datebase', provider => {
    provider.registerDatabaseType({
        text: 'Redis',
        databaseType: 'Redis',
        iconUrl: 'com/fr/plugin/db/redis/images/redis.png',
        edit: 'dec.dcm.connection.plugin.redis.edit',
        show: 'dec.dcm.connection.plugin.redis.show',
    });
});
```

编辑页面实现（`edit` 对应的组件）：

```js
// Redis插件编辑页面
const RedisEditName = 'dec.dcm.connection.plugin.redis.edit';
const RedisEdit = BI.inherit(BI.Widget, {
    props: {
        value: {
            url: '',
            port: '6379',
            password: '',
        },
    },
    render() {
        const o = this.options;

        return {
            type: 'bi.vertical',
            hgap: 15,
            vgap: 10,
            items: [
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '数据库地址',
                        },
                        {
                            type: 'bi.text_editor',
                            width: 300,
                            allowBlank: true,
                            ref: _ref => {
                                this.url = _ref;
                            },
                            text: o.value.url,
                        },
                    ],
                },
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '端口号',
                        },
                        {
                            type: 'bi.text_editor',
                            width: 300,
                            allowBlank: true,
                            ref: _ref => {
                                this.port = _ref;
                            },
                            text: o.value.port,
                        },
                    ],
                },
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '密码',
                        },
                        {
                            type: 'bi.text_editor',
                            width: 300,
                            allowBlank: true,
                            inputType: 'password',
                            ref: _ref => {
                                this.password = _ref;
                            },
                            text: o.value.password,
                        },
                    ],
                },
            ],
        };
    },
    getSubmitValue() {
        return {
            url: this.url.getValue(),
            port: this.port.getValue(),
            password: this.password.getValue(),
        };
    },
});

BI.shortcut(RedisEditName, RedisEdit);
BI.constant('dec.constant.database.conf.connect.form.Redis.edit', RedisEditName);
```

预览页面实现（`show` 对应的组件）：

```js
// Redis插件预览页面
const RedisShowName = 'dec.dcm.connection.plugin.redis.show';
const RedisShow = BI.inherit(BI.Widget, {
    props: {
        value: {
            url: '',
            port: '6379',
            password: '',
        },
    },
    render() {
        const o = this.options;

        return {
            type: 'bi.vertical',
            hgap: 15,
            vgap: 10,
            items: [
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '数据库地址',
                        },
                        {
                            type: 'bi.label',
                            text: o.value.url,
                        },
                    ],
                },
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '端口号',
                        },
                        {
                            type: 'bi.label',
                            text: o.value.port,
                        },
                    ],
                },
                {
                    type: 'bi.left',
                    items: [
                        {
                            type: 'bi.label',
                            cls: 'bi-font-bold',
                            width: 100,
                            textAlign: 'left',
                            text: '密码',
                        },
                        {
                            type: 'bi.label',
                            text: o.value.password,
                        },
                    ],
                },
            ],
        };
    },
});

BI.shortcut(RedisShowName, RedisShow);
BI.constant('dec.constant.database.conf.connect.form.Redis.show', RedisShowName);
```

## 效果

示例工程：[https://git.fanruan.com/fanruan/demo-tabledata-redis](https://git.fanruan.com/fanruan/demo-tabledata-redis)

## 注意事项

编辑页面需要提供 `getValue()` 方法用于获取表单的值。

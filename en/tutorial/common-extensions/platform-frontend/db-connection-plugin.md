# New Database Connection Plugin Interface

## Interface Purpose

The new data connection management page supports adding data connections via plugins, making it easy to extend additional connection types when the built-in options are insufficient.

## Provider

`dec.connection.provider.datebase`

## Example

```js
// Register the Redis plugin
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

Edit page implementation (component corresponding to `edit`):

```js
// Redis plugin edit page
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
                            text: 'Database Address',
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
                            text: 'Port',
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
                            text: 'Password',
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

Preview page implementation (component corresponding to `show`):

```js
// Redis plugin preview page
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
                            text: 'Database Address',
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
                            text: 'Port',
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
                            text: 'Password',
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

## Result

Demo project: [https://git.fanruan.com/fanruan/demo-tabledata-redis](https://git.fanruan.com/fanruan/demo-tabledata-redis)

## Notes

The edit page must provide a `getValue()` method to retrieve the form values.

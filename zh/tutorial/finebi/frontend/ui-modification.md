# UI 修改类

FineBI 6.0 提供的前端接口都以 provider 的形式提供。UI 修改类 provider 用于在仪表板、数据集等界面注入自定义 UI 元素。

---

## `bi.provider.conf.list.report.item`

向主题下方的仪表板 tab 下拉项中注入自定义内容（仪表板编辑界面有效）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addReportOperatorItem` | `addReportOperatorItem(items: any[], position: number = 0)` | 在仪表板 tab 下拉的指定位置插入选项 |

![仪表板 tab 下拉项示意](https://kms.fineres.com/download/attachments/564369588/image2023-6-11_23-59-51.png?version=1&modificationDate=1686499190000&api=v2)

**示例：** 在当前下拉的第 3 项之后插入一个选项

```js
BI.config('bi.provider.conf.list.report.item', (provider) => {
    provider.addReportOperatorItem([{
        text: "我是插入项",
        cls: 'inject-font',
        handler: function () {
            const refresh = this.attr("refresh"); // 获取刷新仪表板的方法
            const { id } = this.attr("reportInfo"); // 获取仪表板相关信息
            // do something
            refresh(); // 根据需要调用刷新
        },
    }], 3)
})
```

---

## `bi.provider.conf.list.widget.item`

向主题下方的组件 tab 下拉项中注入自定义内容（仪表板编辑界面有效）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addWidgetOperatorItem` | `addWidgetOperatorItem(items: any[], position: number = 0)` | 在组件 tab 下拉的指定位置插入选项 |

![组件 tab 下拉项示意](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-1-20.png?version=1&modificationDate=1686499279000&api=v2)

**示例：** 在当前下拉的第 3 项之后插入一个选项

```js
BI.config('bi.provider.conf.list.widget.item', (provider) => {
    provider.addWidgetOperatorItem([{
        text: "我是插入项",
        cls: 'inject-font',
        handler: function () {
            const refresh = this.attr("refresh"); // 获取刷新组件的方法
            const { id } = this.attr("widgetInfo"); // 获取组件相关信息
            // do something
            refresh(); // 根据需要调用刷新
        },
    }], 3)
})
```

---

## `bi.provider.conf.toolbars_analysis`

增加自助数据集右上角的按钮组（自助数据集编辑界面有效）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | 注入按钮项 |

![自助数据集右上角按钮组](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-5-23.png?version=1&modificationDate=1686499521000&api=v2)

```js
BI.config('bi.provider.conf.toolbars_analysis', (provider) => {
    provider.addTooItems([{
        type: "bi.button",
        text: "注入按钮",
        handler: () => { BI.Msg.alert('1') }
    }]);
})
```

---

## `bi.provider.conf.toolbars.analysis_preview`

在自助数据集表预览的预览按钮处注入添加其他 UI。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | 注入 UI 项 |

![自助数据集预览按钮注入](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-14-24.png?version=1&modificationDate=1686500063000&api=v2)

```js
BI.config('bi.provider.conf.toolbars.analysis_preview', (provider) => {
    provider.addTooItems([{
        type: "bi.button",
        text: "注入按钮",
        handler: () => { BI.Msg.alert('1') }
    }]);
})
```

---

## `bi.provider.conf.toolbars`

在公共数据界面右上角【关联视图 多路径设置 更新任务管理 全局更新】处添加新项及点击后的弹出层。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | 注入工具栏项 |

![公共数据界面右上角工具栏](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-15-10.png?version=1&modificationDate=1686500109000&api=v2)

```js
BI.config('bi.provider.conf.toolbars', (provider) => {
    provider.inject([
        {
            type: 'bi.button',
            value: 10, // 不与当前几个按钮的 value 相同即可
            openLayerType: 'bi.xxx.layer', // 点击后的弹出层
        },
    ]);
})
```

---

## `bi.provider.conf.pack.table.toolbars`

注入或过滤表格工具栏配置。

| 方法 | 签名 | 说明 |
|---|---|---|
| `inject` | `inject()` | 注入配置 |
| `filter` | `filter()` | 过滤配置 |

![表格工具栏](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_14-27-11.png?version=1&modificationDate=1686551229000&api=v2)

```js
BI.config('bi.provider.conf.toolbars', (provider) => {
    provider.inject([{
        type: 'edit_custom_table',
        content: ""
    }], [key1]);
    provider.filter("my-key");
})
```

---

## `bi.provider.detail.widget.down_list_item`

向明细表工具栏列表中添加自定义项（仪表板编辑、预览界面均有效）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `insertBefore` | `insertBefore(item: Obj)` | 往最前面添加项 |
| `push` | `push(items: Obj)` | 向最后添加项 |

![明细表工具栏列表](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-17-35.png?version=1&modificationDate=1686500253000&api=v2)

```js
BI.config('bi.provider.detail.widget.down_list_item', (provider) => {
    provider.insertBefore({
        type: 'bi.icon_button',
        cls: 'icon-font',
        handler: function() {
            console.log('1');
        }
    });
})
```

---

## `bi.provider.design.widget_dashboard_toolbar.more_item`

向仪表板上方工具栏的更多区域添加自定义项（仪表板编辑界面有效）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `insertBefore` | `insertBefore(items: Obj)` | 往最前面添加项 |
| `push` | `push(items: Obj)` | 向最后添加项 |

![仪表板工具栏更多区域](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-18-40.png?version=1&modificationDate=1686500319000&api=v2)

```js
BI.config("bi.provider.design.widget_dashboard_toolbar.more_item", (provider) => {
    provider.push([{
        value: De.xtype,
        cls: "data-sentinel-font",
        text: BI.i18nText("Data-Sentinel_Create_Sentinel"),
        title: BI.i18nText("Data-Sentinel_Create_Sentinel"),
        operator: function() {
            // do something
        }
    }])
});
```

---

## `bi.provider.dashboard_toolbar`

向仪表板上方工具栏区域添加自定义项（编辑界面）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | 添加注入项 |

![仪表板工具栏-编辑界面](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-19-31.png?version=1&modificationDate=1686500369000&api=v2)

```js
BI.config('bi.provider.dashboard_toolbar', (provider) => {
    provider.addToolItems({
        type: 'bi.icon_button',
        cls: 'icon-font',
        handler: function() {
            console.log('1');
        }
    });
})
```

---

## `bi.provider.show.dashboard_toolbar`

向仪表板上方工具栏区域添加自定义项（预览界面）。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | 添加注入项 |

![仪表板工具栏-预览界面](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-20-0.png?version=1&modificationDate=1686500398000&api=v2)

```js
BI.config('bi.provider.show.dashboard_toolbar', (provider) => {
    provider.addToolItems({
        type: 'bi.icon_button',
        cls: 'icon-font',
        handler: function() {
            console.log('1');
        }
    });
})
```

---

## `bi.provider.design.widget_dashboard_toolbar`

向组件详细设置上方工具栏处添加自定义项。

| 方法 | 签名 | 说明 |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | 添加注入项 |

![组件详细设置工具栏](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-21-7.png?version=1&modificationDate=1686500466000&api=v2)

```js
BI.config('bi.provider.design.widget_dashboard_toolbar', (provider) => {
    provider.addToolItems({
        type: 'bi.icon_button',
        cls: 'icon-font',
        handler: function() {
            console.log('1');
        }
    });
})
```

---

## `bi.provider.design.widget.detail`

添加列表收起按钮。

| 方法 | 签名 | 说明 |
|---|---|---|
| `injectCollapseWidget` | `injectCollapseWidget(func: Function)` | 注入收起按钮组件 |

```js
BI.config('bi.provider.design.widget.detail', function (provider) {
    provider.injectCollapseWidget(function (obj) {
        return {
            el: {
                type: 'bi.absolute',
                css: {
                    'z-index': 1,
                },
                items: [
                    {
                        el: {
                            type: BI.Layout.xtype,
                            cls: 'bi-plugin-push-button-background',
                            width: 90,
                            height: 25,
                        },
                        top: 32,
                        left: -32,
                        bottom: 0,
                        right: 0,
                    },
                    {
                        el: {
                            type: "bi.icon_change_button",
                            iconCls: 'next-page-h-font',
                            listeners: [{
                                eventName: 'EVENT_CHANGE',
                                action: function () {
                                    this.setIcon(this.isSelected() ? 'pre-page-h-font' : 'next-page-h-font');
                                    if (this.isSelected()) {
                                        obj.expand();
                                    } else {
                                        obj.collapse();
                                    }
                                }
                            }],
                            mounted: function () {
                                obj.collapse();
                            }
                        },
                        top: 0,
                        right: 5,
                        bottom: 0,
                        left: 0,
                    },
                ],
            },
            top: 0,
            right: -25,
            width: 25,
            height: 90,
        }
    });
});
```
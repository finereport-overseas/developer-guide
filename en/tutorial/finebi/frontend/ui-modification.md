# UI Modification Providers

All frontend interfaces provided by FineBI 6.0 are exposed in the form of providers. UI modification providers are used to inject custom UI elements into the dashboard, dataset, and other interfaces.

---

## `bi.provider.conf.list.report.item`

Inject custom items into the dashboard tab dropdown under a theme (effective in the dashboard edit view).

| Method | Signature | Description |
|---|---|---|
| `addReportOperatorItem` | `addReportOperatorItem(items: any[], position: number = 0)` | Insert options at a specified position in the dashboard tab dropdown |

![Dashboard tab dropdown](https://kms.fineres.com/download/attachments/564369588/image2023-6-11_23-59-51.png?version=1&modificationDate=1686499190000&api=v2)

**Example:** Insert an option after the 3rd item in the current dropdown

```js
BI.config('bi.provider.conf.list.report.item', (provider) => {
    provider.addReportOperatorItem([{
        text: "Injected Item",
        cls: 'inject-font',
        handler: function () {
            const refresh = this.attr("refresh"); // Get the method to refresh the dashboard
            const { id } = this.attr("reportInfo"); // Get dashboard-related information
            // do something
            refresh(); // Call refresh as needed
        },
    }], 3)
})
```

---

## `bi.provider.conf.list.widget.item`

Inject custom items into the component tab dropdown under a theme (effective in the dashboard edit view).

| Method | Signature | Description |
|---|---|---|
| `addWidgetOperatorItem` | `addWidgetOperatorItem(items: any[], position: number = 0)` | Insert options at a specified position in the component tab dropdown |

![Component tab dropdown](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-1-20.png?version=1&modificationDate=1686499279000&api=v2)

**Example:** Insert an option after the 3rd item in the current dropdown

```js
BI.config('bi.provider.conf.list.widget.item', (provider) => {
    provider.addWidgetOperatorItem([{
        text: "Injected Item",
        cls: 'inject-font',
        handler: function () {
            const refresh = this.attr("refresh"); // Get the method to refresh the component
            const { id } = this.attr("widgetInfo"); // Get component-related information
            // do something
            refresh(); // Call refresh as needed
        },
    }], 3)
})
```

---

## `bi.provider.conf.toolbars_analysis`

Add buttons to the top-right button group of the self-service dataset editor (effective in the self-service dataset edit view).

| Method | Signature | Description |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | Inject button items |

![Self-service dataset top-right button group](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-5-23.png?version=1&modificationDate=1686499521000&api=v2)

```js
BI.config('bi.provider.conf.toolbars_analysis', (provider) => {
    provider.addTooItems([{
        type: "bi.button",
        text: "Injected Button",
        handler: () => { BI.Msg.alert('1') }
    }]);
})
```

---

## `bi.provider.conf.toolbars.analysis_preview`

Inject additional UI elements at the preview button area of the self-service dataset table preview.

| Method | Signature | Description |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | Inject UI items |

![Self-service dataset preview button injection](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-14-24.png?version=1&modificationDate=1686500063000&api=v2)

```js
BI.config('bi.provider.conf.toolbars.analysis_preview', (provider) => {
    provider.addTooItems([{
        type: "bi.button",
        text: "Injected Button",
        handler: () => { BI.Msg.alert('1') }
    }]);
})
```

---

## `bi.provider.conf.toolbars`

Add new items and their popup layers to the top-right area of the public data interface [Association View / Multi-path Settings / Update Task Management / Global Update].

| Method | Signature | Description |
|---|---|---|
| `addTooItems` | `addTooItems(items: any[])` | Inject toolbar items |

![Public data interface top-right toolbar](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-15-10.png?version=1&modificationDate=1686500109000&api=v2)

```js
BI.config('bi.provider.conf.toolbars', (provider) => {
    provider.inject([
        {
            type: 'bi.button',
            value: 10, // Must differ from the values of existing buttons
            openLayerType: 'bi.xxx.layer', // Popup layer shown on click
        },
    ]);
})
```

---

## `bi.provider.conf.pack.table.toolbars`

Inject or filter table toolbar configuration.

| Method | Signature | Description |
|---|---|---|
| `inject` | `inject()` | Inject configuration |
| `filter` | `filter()` | Filter configuration |

![Table toolbar](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_14-27-11.png?version=1&modificationDate=1686551229000&api=v2)

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

Add custom items to the detail table toolbar list (effective in both dashboard edit and preview views).

| Method | Signature | Description |
|---|---|---|
| `insertBefore` | `insertBefore(item: Obj)` | Prepend an item to the beginning |
| `push` | `push(items: Obj)` | Append an item to the end |

![Detail table toolbar list](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-17-35.png?version=1&modificationDate=1686500253000&api=v2)

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

Add custom items to the "More" area of the toolbar above the dashboard (effective in the dashboard edit view).

| Method | Signature | Description |
|---|---|---|
| `insertBefore` | `insertBefore(items: Obj)` | Prepend items to the beginning |
| `push` | `push(items: Obj)` | Append items to the end |

![Dashboard toolbar "More" area](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-18-40.png?version=1&modificationDate=1686500319000&api=v2)

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

Add custom items to the toolbar area above the dashboard (edit view).

| Method | Signature | Description |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | Add injected items |

![Dashboard toolbar - edit view](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-19-31.png?version=1&modificationDate=1686500369000&api=v2)

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

Add custom items to the toolbar area above the dashboard (preview view).

| Method | Signature | Description |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | Add injected items |

![Dashboard toolbar - preview view](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-20-0.png?version=1&modificationDate=1686500398000&api=v2)

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

Add custom items to the toolbar above the component detail settings panel.

| Method | Signature | Description |
|---|---|---|
| `addToolItems` | `addToolItems(items: any[])` | Add injected items |

![Component detail settings toolbar](https://kms.fineres.com/download/attachments/564369588/image2023-6-12_0-21-7.png?version=1&modificationDate=1686500466000&api=v2)

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

Add a list collapse button.

| Method | Signature | Description |
|---|---|---|
| `injectCollapseWidget` | `injectCollapseWidget(func: Function)` | Inject a collapse button component |

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

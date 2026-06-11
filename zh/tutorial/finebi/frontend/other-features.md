# 其他功能类

FineBI 6.0 提供的前端接口都以 provider 的形式提供。其他功能类 provider 提供字段默认配置、控件过滤范围、字体设置、跳转 URL 处理等功能。

---

## `bi.provider.design.field.default.setting`

设置从待分析区域拖到分析区域的字段的默认配置（如默认排序方式），组件和控件都支持。目前只支持设置排序。

| 方法 | 签名 | 作用 |
|---|---|---|
| `setDefaultFieldConfig` | `setDefaultFieldConfig(config: Obj)` | 设置字段默认配置 |

**示例：** 字段1在拖到分析区域后默认按照字段2升序

```js
BI.config('bi.provider.design.field.default.setting', function (provider) {
    provider.setDefaultFieldConfig({
        name: '字段1',
        key: 'sort',
        value: {
            type: CUSTOM_FIELD_CONFIG.AES_SORT_TYPE,
            sortTargetName: '字段2'
        }
    })
})
```

---

## `bi.provider.control.widget.range`

设置额外参与控件过滤范围的组件类型。

| 方法 | 签名 | 作用 |
|---|---|---|
| `setControlRangeWidget` | `setControlRangeWidget(type: number)` | 设置参与过滤范围的组件类型 |

**示例：** 设置组件类型为 100 的组件参与控件过滤可选范围

```js
BI.config('bi.provider.control.widget.range', function (provider) {
    provider.setControlRangeWidget(100);
})
```

---

## `bi.provider.template_style`

在 BI 字体设置界面注入额外字体以供选择使用，可拓展修改组件的其他初始样式属性的值。

| 方法 | 签名 | 作用 |
|---|---|---|
| `addFonts` | `addFonts({fonts: Obj[]})` | 注入自定义字体 |

```js
BI.config('bi.provider.template_style', function (provider) {
    provider.addFonts([{
        text: "自定义显示值",
        value: "字体名称"
    }]);
});
```

---

## `bi.provider.jump.url.handler`

自定义跳转 URL 的处理方法。

| 方法 | 签名 | 作用 |
|---|---|---|
| `injectFunc` | `injectFunc(url: string, templateId: string)` | 注入自定义跳转 URL 处理函数 |

```js
BI.config('bi.provider.jump.url.handler', provider => {
    provider.injectFunc((url, templateHelperId) => {
        return myCustomURL;
    });
});
```

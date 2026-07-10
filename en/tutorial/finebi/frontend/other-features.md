# Other Feature Providers

All frontend interfaces provided by FineBI 6.0 are exposed in the form of providers. Other feature providers cover functionality such as default field configuration, control filter scope, font settings, and jump URL handling.

---

## `bi.provider.design.field.default.setting`

Set the default configuration for fields dragged from the "to-be-analyzed" area into the analysis area (e.g., default sort order). Supported by both components and controls. Currently only sorting can be configured.

| Method | Signature | Description |
|---|---|---|
| `setDefaultFieldConfig` | `setDefaultFieldConfig(config: Obj)` | Set the default field configuration |

**Example:** After Field1 is dragged into the analysis area, sort ascending by Field2 by default

```js
BI.config('bi.provider.design.field.default.setting', function (provider) {
    provider.setDefaultFieldConfig({
        name: 'Field1',
        key: 'sort',
        value: {
            type: CUSTOM_FIELD_CONFIG.AES_SORT_TYPE,
            sortTargetName: 'Field2'
        }
    })
})
```

---

## `bi.provider.control.widget.range`

Set additional component types to participate in the control filter scope.

| Method | Signature | Description |
|---|---|---|
| `setControlRangeWidget` | `setControlRangeWidget(type: number)` | Set the component type to include in the filter scope |

**Example:** Include components of type 100 in the control filter selectable range

```js
BI.config('bi.provider.control.widget.range', function (provider) {
    provider.setControlRangeWidget(100);
})
```

---

## `bi.provider.template_style`

Inject additional fonts into the BI font settings interface for selection, and extend or modify other initial style property values of components.

| Method | Signature | Description |
|---|---|---|
| `addFonts` | `addFonts({fonts: Obj[]})` | Inject custom fonts |

```js
BI.config('bi.provider.template_style', function (provider) {
    provider.addFonts([{
        text: "Custom Display Label",
        value: "font-family-name"
    }]);
});
```

---

## `bi.provider.jump.url.handler`

Customize the handler for jump URLs.

| Method | Signature | Description |
|---|---|---|
| `injectFunc` | `injectFunc(url: string, templateId: string)` | Inject a custom jump URL handler function |

```js
BI.config('bi.provider.jump.url.handler', provider => {
    provider.injectFunc((url, templateHelperId) => {
        return myCustomURL;
    });
});
```

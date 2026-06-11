# 信息获取类

FineBI 6.0 提供的前端接口都以 provider 的形式提供。信息获取类 provider 用于获取仪表板、组件的相关信息。

---

## `bi.provider.design.template`

获取仪表板及组件信息。由于 6.0 中仪表板可以在主题页面中同时编辑多个，所以相较于 5.1.x 的 Utils 方法，6.0 的 provider 需要额外提供仪表板 id 来确定要获取哪个仪表板中的信息。

> 如果当前主题下或单页面下只有一个仪表板，id 参数可以缺省。

| 方法 | 签名 | 作用 |
|---|---|---|
| `getAllWidgetIds` | `getAllWidgetIds(id: string)` | 根据仪表板 id 获取仪表板中所有组件 id |
| `getName` | `getName(id: string)` | 根据仪表板 id 获取仪表板名称 |
| `getWidgetTypeById` | `getWidgetTypeById(id: string)` | 根据组件 id 获取组件类型 |
| `getWidgetNameById` | `getWidgetNameById(id: string)` | 根据组件 id 获取组件名称 |
| `isUnderPreviewMode` | `isUnderPreviewMode(id: string)` | 当前模板是否处于预览状态 |
| `getWidgetById` | `getWidgetById(id: string)` | 根据组件 id 获取组件详细配置 |
| `getControlParamsParsedUrl` | `getControlParamsParsedUrl(id: string, url: string)` | 根据仪表板 id 和 url，将 URL 上控件参数格式化 |

**示例**

```js
BI.Providers.getProvider("bi.provider.design.template").getAllWidgetIds('xxxxxxx')
```

**`getControlParamsParsedUrl` 参数格式**

URL 参数的格式为 `目标模板控件名={当前模板控件名}`，解析后将控件值替换到对应位置：

```
// 格式：目标模板控件名={当前模板控件名}
文本下拉={文本下拉}

// 解析结果示例
文本下拉={文本下拉}  ==>  文本下拉=xxx
riqi={日期区间}      ==>  riqi_s=xxx&riqi_e=xxx
```

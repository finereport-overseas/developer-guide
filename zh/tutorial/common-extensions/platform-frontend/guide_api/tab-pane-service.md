# 全局 service — dec.service.frame.tab_pane

## tabPane

获取方法：BI.Services.getService('dec.service.frame.tab_pane')

**方法列表**

- [tabPane~addItem(entry)](#tabpaneadditemententry)
- [tabPane~closeTab(v)](#tabpaneclosetabv)
- [tabPane~closeOtherTabs()](#tabpanecloseotheretabs)
- [tabPane~closeAllTabs()](#tabpaneclosealltabs)
- [tabPane~refreshTab(cardValue, options)](#tabpanerefreshtabcardvalue-options)
- [tabPane~closeActiveTab()](#tabpanecloseactivetab)
- [tabPane~getShowingTab()](#tabpanegetshowingtab)
- [tabPane~fullScreen()](#tabpanefullscreen)
- [tabPane~exitFullScreen()](#tabpaneexitfullscreen)
- [tabPane~openCustomTab(cardName, cardValue, cardType)](#tabpaneopencustomtabcardname-cardvalue-cardtype)
- [tabPane~showTab(value, card)](#tabpaneshortabvalue-card)
- [tabPane~alternatePlay(time, refresh)](#tabpanealternateplaytime-refresh) ⇒ `number`

---

### tabPane~addItem(entry)

添加一个 tab

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| entry | `object` | entry 对象 |
| entry.text | `string` | tab 标题显示文本 |
| entry.id | `string` | 唯一标识 tab 的 id |
| entry.cardType | `string` | tab 所对应的页面 |

### tabPane~closeTab(v)

关闭一个 tab

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| v | `string` | tab 的 value |

### tabPane~closeOtherTabs()

关闭除当前 tab 的其他 tab，首页 tab 不会被关

**Kind**: inner method of `tabPane`

### tabPane~closeAllTabs()

关闭所有 tab，首页 tab 不会被关

**Kind**: inner method of `tabPane`

### tabPane~refreshTab(cardValue, options)

刷新一个 tab

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| cardValue | `string` | tab 的 id |
| options | `object` | tab 的配置信息 |

### tabPane~closeActiveTab()

关闭当前激活的 tab

**Kind**: inner method of `tabPane`

### tabPane~getShowingTab()

获取当前激活 tab 的 id

**Kind**: inner method of `tabPane`

### tabPane~fullScreen()

全屏显示

**Kind**: inner method of `tabPane`

### tabPane~exitFullScreen()

退出全屏

**Kind**: inner method of `tabPane`

### tabPane~openCustomTab(cardName, cardValue, cardType)

显式地打开一个 tab

**Kind**: inner method of `tabPane`

| Param |
| --- |
| cardName |
| cardValue |
| cardType |

### tabPane~showTab(value, card)

切换 tab

**Kind**: inner method of `tabPane`

| Param | Description |
| --- | --- |
| value | tab 的 value 值 |
| card | 可选更新的 card 信息 |

### tabPane~alternatePlay(time, refresh) ⇒ `number`

启动轮播

**Kind**: inner method of `tabPane`

| Param | Description |
| --- | --- |
| time | 间隔时间，单位秒 |
| refresh | 切换时是否需要刷新 |

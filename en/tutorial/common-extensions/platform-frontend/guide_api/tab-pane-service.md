# Global Service — dec.service.frame.tab_pane

## tabPane

Access method: BI.Services.getService('dec.service.frame.tab_pane')

**Method List**

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

Add a tab.

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| entry | `object` | The entry object |
| entry.text | `string` | Display text for the tab title |
| entry.id | `string` | Unique identifier for the tab |
| entry.cardType | `string` | The page associated with the tab |

### tabPane~closeTab(v)

Close a tab.

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| v | `string` | The value of the tab |

### tabPane~closeOtherTabs()

Close all tabs except the current one. The home tab will not be closed.

**Kind**: inner method of `tabPane`

### tabPane~closeAllTabs()

Close all tabs. The home tab will not be closed.

**Kind**: inner method of `tabPane`

### tabPane~refreshTab(cardValue, options)

Refresh a tab.

**Kind**: inner method of `tabPane`

| Param | Type | Description |
| --- | --- | --- |
| cardValue | `string` | The id of the tab |
| options | `object` | Configuration options for the tab |

### tabPane~closeActiveTab()

Close the currently active tab.

**Kind**: inner method of `tabPane`

### tabPane~getShowingTab()

Get the id of the currently active tab.

**Kind**: inner method of `tabPane`

### tabPane~fullScreen()

Enter full-screen mode.

**Kind**: inner method of `tabPane`

### tabPane~exitFullScreen()

Exit full-screen mode.

**Kind**: inner method of `tabPane`

### tabPane~openCustomTab(cardName, cardValue, cardType)

Explicitly open a tab.

**Kind**: inner method of `tabPane`

| Param |
| --- |
| cardName |
| cardValue |
| cardType |

### tabPane~showTab(value, card)

Switch to a tab.

**Kind**: inner method of `tabPane`

| Param | Description |
| --- | --- |
| value | The value of the tab |
| card | Optional card info to update |

### tabPane~alternatePlay(time, refresh) ⇒ `number`

Start carousel/slideshow playback.

**Kind**: inner method of `tabPane`

| Param | Description |
| --- | --- |
| time | Interval duration in seconds |
| refresh | Whether to refresh when switching tabs |

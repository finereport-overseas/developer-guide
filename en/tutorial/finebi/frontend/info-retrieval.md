# Information Retrieval Providers

All frontend interfaces provided by FineBI 6.0 are exposed in the form of providers. Information retrieval providers are used to obtain information about dashboards and components.

---

## `bi.provider.design.template`

Retrieve dashboard and component information. Because FineBI 6.0 allows multiple dashboards to be edited simultaneously within a theme page, these providers require an additional dashboard `id` parameter to identify the target dashboard — unlike the Utils methods in 5.1.x.

> If there is only one dashboard under the current theme or single page, the `id` parameter can be omitted.

| Method | Signature | Description |
|---|---|---|
| `getAllWidgetIds` | `getAllWidgetIds(id: string)` | Get all component IDs in a dashboard by dashboard ID |
| `getName` | `getName(id: string)` | Get the dashboard name by dashboard ID |
| `getWidgetTypeById` | `getWidgetTypeById(id: string)` | Get the component type by component ID |
| `getWidgetNameById` | `getWidgetNameById(id: string)` | Get the component name by component ID |
| `isUnderPreviewMode` | `isUnderPreviewMode(id: string)` | Check whether the current template is in preview mode |
| `getWidgetById` | `getWidgetById(id: string)` | Get the detailed configuration of a component by component ID |
| `getControlParamsParsedUrl` | `getControlParamsParsedUrl(id: string, url: string)` | Parse control parameter values in a URL using the dashboard ID and URL |

**Example**

```js
BI.Providers.getProvider("bi.provider.design.template").getAllWidgetIds('xxxxxxx')
```

**`getControlParamsParsedUrl` parameter format**

The URL parameter format is `targetControlName={currentControlName}`. After parsing, the control value is substituted into the corresponding position:

```
// Format: targetControlName={currentControlName}
TextDropdown={TextDropdown}

// Parsed result example
TextDropdown={TextDropdown}  ==>  TextDropdown=xxx
riqi={DateRange}             ==>  riqi_s=xxx&riqi_e=xxx
```

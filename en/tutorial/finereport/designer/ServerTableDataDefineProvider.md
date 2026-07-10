# ServerTableDataDefineProvider

| Property | Value |
| --- | --- |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.ServerTableDataDefineProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ServerTableDataDefineProvider) |

---

## 1. Special Terms

None

## 2. Background and Use Cases

The standard FineReport product only supports data queries based on standard JDBC or JNDI, along with a few basic file-based data sources. Connecting to non-standard JDBC databases, WebService, REST, real-time data sources, RPC, and other data interface systems requires using dataset interfaces.

Most developers have experience with the basic [program dataset](https://help.fanruan.com/finereport/doc-view-637.html) approach. While program datasets can satisfy most data needs technically, they have an obvious usability shortcoming for report creators: creators must remember many class names and business associations, or add large numbers of parameters to enable "configuration," while still being unable to prevent those parameters from being injected via URL or the parameter panel (e.g., sensitive configurations that should only be set at template creation time).

To provide report creators with a better user experience and improved security, the product introduced the `ServerTableDataDefineProvider` interface, allowing developers to provide dataset extensions in plugin form. Note that unlike `TableDataDefineProvider`, this interface only applies to editing **server datasets** in the designer.

## 3. Interface Introduction

```java
package com.fr.design.fun;

/**
 * @author : richie
 * @since : 8.0
 * Interface for custom server dataset design UIs.
 */
public interface ServerTableDataDefineProvider extends TableDataDefineProvider {

    String XML_TAG = "ServerTableDataDefineProvider";
}
```

TableDataDefineProvider

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |
| BI | 3.6 | Yes |  |
| BI | 4.0 | Yes |  |
| BI | 5.1 | Yes |  |
| BI | 5.1.2 | Yes |  |
| BI | 5.1.3 | Yes |  |

## 5. Plugin Registration

```xml
<extra-designer>
        <ServerTableDataDefineProvider class="your class name"/>
</extra-designer>
```

## 6. How It Works

When the dataset type selection list is triggered, `TableDataCreatorProducer#createReportTableDataCreator` is called. At that point, all `ServerTableDataDefineProvider` instances declared in plugins are read. Since server datasets are added in the designer while the actual dataset computation is a server-side concern, `ServerTableDataDefineProvider` also serves to decouple these concerns. For a specific server dataset, only the `tabledata` class name is ultimately saved to `finedb` (note: in version 9.0 and below, it was saved to `datasources.xml`).

## 7. Limitations

`iconPathForTableData`: The returned icon path (e.g., `/com/demo/hg/resources/images/main.png`) must be a 16x16 px icon, prepared by the developer. The interface does not perform uniform scaling, so non-16x16 icons will look inconsistent.

`classForTableData`: Must return a `TableData` interface class. For convenience and stability, developers are required to extend `AbstractParameterTableData`. [Note]

`classForInitTableData`: If `classForTableData` is already implemented, this method currently has no substantive effect. However, it is generally expected that developers return the same class as `classForTableData`.

## 8. Useful Links

Demo: [demo-table-data-define](https://code.fanruan.com/hugh/demo-table-data-define)

TableDataDefineProvider

UniversalServerTableDataProvider

## 9. Open Source Examples

Disclaimer: All open-source examples in the documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide instruction or guidance on any outcomes related to open-source examples. Any commercial use is entirely at the user's own risk.

[demo-tabledata-redis](https://code.fanruan.com/fanruan/demo-tabledata-redis)

# TableDataDefineProvider

| Property | Value |
| --- | --- |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.TableDataDefineProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/TableDataDefineProvider) |

---

## 1. Special Terms

None

## 2. Background and Use Cases

The standard FineReport product only supports data queries based on standard JDBC or JNDI, along with a few basic file-based data sources. Connecting to non-standard JDBC databases, WebService, REST, real-time data sources, RPC, and other data interface systems requires using dataset interfaces.

Most developers have experience with the basic [program dataset](https://help.fanruan.com/finereport/doc-view-637.html) approach. While program datasets can satisfy most data needs technically, they have a notable usability shortcoming for report creators: creators must remember many class names and business associations, or add large numbers of parameters to enable "configuration," while still being unable to prevent those parameters from being injected via URL or the parameter panel (e.g., sensitive configurations that should only be set at template creation time).

To provide report creators with a better user experience and improved security, the product introduced the `TableDataDefineProvider` interface, allowing developers to provide dataset extensions in plugin form. Note that unlike `ServerTableDataDefineProvider`, this interface only applies to editing **template datasets** in the designer.

## 3. Interface Introduction

```java
package com.fr.design.fun;

import com.fr.base.TableData;
import com.fr.design.data.tabledata.tabledatapane.AbstractTableDataPane;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.fun.mark.Aftermath;

/**
 * @author : richie
 * @since : 7.1.1
 * Interface for custom report dataset UI — individual standalone datasets.
 */
public interface TableDataDefineProvider extends Mutable, Aftermath {

    String XML_TAG = "TableDataDefineProvider";

    int CURRENT_LEVEL = 1;

    /**
     * The dataset type corresponding to the custom dataset settings UI.
     * @return dataset type
     */
    Class<? extends TableData> classForTableData();

    /**
     * The initial dataset type for the custom dataset settings UI.
     * Effective when a single dataset has multiple implementations.
     * @return dataset type
     */
    Class<? extends TableData> classForInitTableData();

    /**
     * The UI class corresponding to the custom dataset settings.
     * @return dataset UI class
     */
    Class<? extends AbstractTableDataPane> appearanceForTableData();

    /**
     * The display name of the custom dataset settings in the menu.
     * @return name
     */
    String nameForTableData();

    /**
     * Name prefix used when a new custom dataset is created.
     * @return name prefix
     */
    String prefixForTableData();

    /**
     * The icon displayed in the menu for the custom dataset.
     * @return icon path
     */
    String iconPathForTableData();
}
```

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
        <TableDataDefineProvider class="your class name"/>
</extra-designer>
```

## 6. How It Works

When the dataset type selection list is triggered, `TableDataCreatorProducer#createReportTableDataCreator` is called. At that point, all `TableDataDefineProvider` instances declared in plugins are read. Since datasets are added in the designer while the actual dataset computation is a server-side concern, `TableDataDefineProvider` also serves to decouple these concerns. For a specific dataset, only the `tabledata` class name is ultimately saved to the `.cpt`/`.frm` file.

## 7. Limitations

`iconPathForTableData`: The returned icon path (e.g., `/com/demo/hg/resources/images/main.png`) must be a 16x16 px icon, prepared by the developer. The interface does not perform uniform scaling, so non-16x16 icons will look inconsistent.

`classForTableData`: Must return a `TableData` interface class. For convenience and stability, developers are required to extend `AbstractParameterTableData`. [Note]

`classForInitTableData`: If `classForTableData` is already implemented, this method currently has no substantive effect. However, it is generally expected that developers return the same class as `classForTableData`.

## 8. Useful Links

Demo: [demo-table-data-define](https://code.fanruan.com/hugh/demo-table-data-define)

ServerTableDataDefineProvider

UniversalServerTableDataProvider

## 9. Open Source Examples

Disclaimer: All open-source examples in the documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide instruction or guidance on any outcomes related to open-source examples. Any commercial use is entirely at the user's own risk.

[demo-tabledata-redis](https://code.fanruan.com/fanruan/demo-tabledata-redis)

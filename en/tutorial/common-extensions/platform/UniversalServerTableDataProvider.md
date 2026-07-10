# UniversalServerTableDataProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.UniversalServerTableDataProvider` |

## Background and Use Cases

Decision platform 10.0 introduced platform-side configuration management for datasets and data sources, solving the previous requirement of connecting remotely via the designer or using import/export to manage datasets. This interface provides platform-side management capabilities for dataset types extended by plugins.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.base.TableData;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.fun.mark.WebCoalition;

public interface UniversalServerTableDataProvider<T extends TableData>
        extends WebCoalition, Mutable {

    String MARK_STRING = "UniversalServerTableDataProvider";
    int CURRENT_LEVEL = 1;

    Class<T> classForTableData();
    String nameForTableData();
    JSONObject serialize(T tableData);
    T deserialize(T oldDataSet, JSONObject object);
}
```

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <UniversalServerTableDataProvider class="your class name"/>
</extra-decision>
```

## How It Works

`DataSetProcessorFactory` loads all plugin-declared platform server-side dataset interfaces during initialization. `DataSetResource` manages CRUD operations through the `/{version}/dataset` route.

## Notes

- `classForTableData` must return a TableData interface; it is recommended to extend `AbstractParameterTableData`
- `nameForTableData` must match the dataset type declaration on the frontend
- `serialize`/`deserialize` handle conversion between dataset instances and their JSON representation
- The frontend `cardType` references a custom FineUI component for UI binding

## Useful Links

- Demo: [demo-table-data-define](https://code.fanruan.com/hugh/demo-table-data-define)

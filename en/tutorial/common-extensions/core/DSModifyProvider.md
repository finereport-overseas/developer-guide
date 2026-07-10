# DSModifyProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.DSModifyProvider` |

## Background and Use Cases

This interface is used to perform secondary processing on data after all dataset types have finished retrieving their data. It supports post-processing for both built-in datasets and third-party plugin datasets. Common use cases include: data splitting (parsing JSON/XML fields), data masking, basic aggregation, and execution monitoring.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.base.TableData;
import com.fr.general.data.DataModel;
import com.fr.script.Calculator;
import com.fr.stable.fun.mark.Mutable;

public interface DSModifyProvider extends Mutable {

    String MARK_STRING = "DSModifyProvider";

    /**
     * Validates the dataset format and determines whether secondary processing is needed.
     * Multiple implementations can coexist.
     */
    boolean accept(TableData ds, Calculator cal, DataModel old);

    /**
     * Performs secondary processing on the dataset.
     * Transforms the initial result and returns the modified dataset.
     */
    DataModel modify(TableData ds, Calculator cal, DataModel old);
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 3.6 | Supported |
| BI | 4.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <DSModifyProvider class="your class name"/>
</extra-core>
```

## How It Works

All dataset calculations pass through two methods: `EmbeddedTableData#createDataModel` (designer preview) and `SynchronizedLiveDataModelUtils.getLiveDataModel` (server-side calculation). The system retrieves all registered adapters via `PluginModule` and applies the modification logic in order based on the result of each `accept()` check.

## Useful Links

- Demo: [demo-ds-modify-provider](https://code.fanruan.com/hugh/demo-ds-modify-provider)
- Open source reference: [demo-tabledata-modify](https://code.fanruan.com/fanruan/demo-tabledata-modify)

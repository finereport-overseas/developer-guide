# TableDataBuildProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.TableDataBuildProvider` |

## Background and Use Cases

This interface is suited for dataset execution monitoring and transformation scenarios — for example, converting large numbers of the same dataset type into a more optimized and secure dataset type for computation, or implementing unified parameter pre-processing and execution monitoring across all datasets.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.base.TableData;
import com.fr.stable.fun.mark.Mutable;

public interface TableDataBuildProvider<T extends TableData> extends Mutable {

    int CURRENT_LEVEL = 1;
    String MARK_STRING = "TableDataBuildProvider";

    boolean matchPattern(String tdClassName);
    Class<? extends TableData> getAttrClass();
    Class<T> getBuildClass();
    T newInstance() throws Exception;
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <TableDataBuildProvider class="your class name"/>
</extra-core>
```

## How It Works

This interface takes effect during the XML-to-object deserialization process when a template file loads its dataset configuration. The system retrieves all registered dataset conversion providers, identifies datasets that need transformation via `matchPattern`, then calls `newInstance` to create a new dataset instance.

## Useful Links

- Demo: [demo-table-data-build-provider](https://code.fanruan.com/hugh/demo-table-data-build-provider)

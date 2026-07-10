# TableDataProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.TableDataProvider` |

## Background and Use Cases

FanRuan Report supports data query datasets with parameterized and expression-parsed queries; frontend parameters can be injected into SQL fragments, which poses an SQL injection risk. Version 10.0 includes built-in injection prevention, but users may still need custom interception handling and data query monitoring. `TableDataProvider` is the interface opened to meet these needs.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.ParameterProvider;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

/**
 * Dataset parameter pre-processing
 */
public interface TableDataProvider extends Mutable {

    String XML_TAG = "TableDataProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Modifies parameter values before SQL parsing
     */
    void processParametersBeforeAnalyzeSQL(ParameterProvider[] paras,
                                           CalculatorProvider calculatorProvider);

    /**
     * Modifies parameters and SQL before dataset execution
     */
    String processTableDataSQL(ParameterProvider[] paras, String sql,
                               CalculatorProvider calculatorProvider);
}
```

## Supported Versions

| Product | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported | Interface named TableDataProcessor; single instance only |
| FR | 9.0 | Supported | Interface named TableDataProcessor; single instance only |
| FR | 10.0 | Supported | — |
| FR | 11.0 | Supported | |
| BI | 3.6 | Supported | Interface named TableDataProcessor; single instance only |
| BI | 4.0 | Supported | Interface named TableDataProcessor; single instance only |
| BI | 5.1 | Supported | — |
| BI | 5.1.2 | Supported | — |
| BI | 5.1.3 | Supported | — |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <TableDataProvider class="your class name"/>
</extra-core>
```

## How It Works

During `DBTableData` result computation, the system pre-processes parameters and SQL via `getStatementSql`, retrieves all registered provider instances, and applies the pre-processing operations in sequence.

**Limitation:** This interface only applies to data query datasets or datasets that use the `PluginModule` interface retrieval mechanism.

## Useful Links

- Demo: [demo-table-data-provider](https://code.fanruan.com/hugh/demo-table-data-provider)

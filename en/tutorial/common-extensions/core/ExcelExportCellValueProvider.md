# ExcelExportCellValueProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.ExcelExportCellValueProvider` |

## Background and Use Cases

This interface is suited for scenarios that require targeted adjustments to the export logic (rather than a complete rewrite), allowing developers to process exported values when exporting to Excel. A typical use case is numeric format conversion (e.g., converting 10000 to "10K").

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

public interface ExcelExportCellValueProvider extends Mutable {
    String XML_TAG = "ExcelExportCellValueProvider";
    int CURRENT_LEVEL = 1;

    Object getCellValue(Object cellElement, Object oriValue,
                        CalculatorProvider ca);
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
    <ExcelExportCellValueProvider class="your class name"/>
</extra-core>
```

## How It Works

This interface operates at the base layer of the export processing pipeline. If an upper-level export customization bypasses `AbstractExcelExporter#evalCellValue`, developers must invoke this interface manually. Multiple value-modification interfaces may take effect simultaneously; developers should determine which values need conversion.

## Useful Links

- Demo: [demo-excel-export-cell-value](https://code.fanruan.com/hugh/demo-excel-export-cell-value)

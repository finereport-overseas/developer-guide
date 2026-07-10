# PrintCellValueProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.PrintCellValueProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/PrintCellValueProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

The `PrintCellValueProvider` interface is primarily used to modify the result value of a cell during report printing. Common scenarios include converting special rich text content to an image for image-based output, or desensitizing cell content during printing.

## 3. Interface Overview


```java
/*
 * Copyright (c) 2001-2014,FineReport Inc, All Rights Reserved.
 */

package com.fr.report.fun;

import com.fr.report.cell.cellattr.CellGUIAttr;
import com.fr.report.cell.cellattr.PageExportCellElement;
import com.fr.stable.fun.mark.Mutable;

/**
 * Interface for applying special processing to specific cell content during printing
 */
public interface PrintCellValueProvider extends Mutable {

    String MARK_STRING = "PrintCellValueProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Applies special processing to a cell
     * @param cellElement Cell element
     * @param cellGUIAttr Cell display attributes
     * @param oriValue    Original cell value
     * @param width       Cell width
     * @param height      Cell height
     * @return Processed cell value
     */
    Object getCellValue(PageExportCellElement cellElement, CellGUIAttr cellGUIAttr, Object oriValue, int width, int height);

}
```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
	<PrintCellValueProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

Where needed, all plugin-declared cell print value modification interface instances are retrieved via:
`Set<PrintCellValueProvider> providers = ExtraReportClassManager.getInstance().getArray(PrintCellValueProvider.MARK_STRING);`

In the product, this is applied primarily within `PagePainter`.

## 7. Constraints and Notes

None

## 8. Useful Links

Demo: [demo-print-cell-value-provider](https://code.fanruan.com/hugh/demo-print-cell-value-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

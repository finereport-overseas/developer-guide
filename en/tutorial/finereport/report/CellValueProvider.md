# CellValueProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.CellValueProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/CellValueProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

The `CellValueProvider` interface is used to modify the actual value of a cell during template calculation, adjust the physical size of a cell during calculation, and modify the display value rendered in an HTML page (not the cell's display value itself).

Common use cases include:

1. Bulk term escaping and internationalization for display-only reports, converting large numbers of cell values.
2. Cell value formatting (primarily used in early versions before rich text was available, for document-style reports).
3. Adjusting cell dimensions for cells containing special value types.
4. Differentiating display content from export content at scale (scenarios where the on-screen and exported content must differ).

## 3. Interface Overview


```java
package com.fr.report.fun;


import com.fr.base.Style;
import com.fr.report.cell.CellElement;
import com.fr.report.cell.cellattr.CellGUIAttr;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

/**
 * Cell intermediate processor
 */
public interface CellValueProvider extends Mutable{
    String MARK_STRING = "CellValueProvider";
	int CURRENT_LEVEL = 1;

	/**
	 * Gets the cell value
	 * 
	 * @param oriValue Uncalculated original cell value
	 * @param ca       Calculator
	 * @return Calculated cell value
	 */
    Object process(Object oriValue, CalculatorProvider ca);

    /**
     * Processes the cell value before converting to HTML.
     * For example, if you do not want to use the default display method when outputting a cell
     * to the web, you can convert it to an image, rich text, etc.
     *
     * @param guiAttr  Display attributes
     * @param height   Cell height
     * @param oriValue Original value
     * @param style    Style
     * @param width    Cell width
     * @return Processed value
     */
    Object processBeforeToTag(Object oriValue, CellGUIAttr guiAttr, Style style, int width, int height);
    
    /**
     * Calculates the actual height and width of this cell
     * @param cellElement Cell element
     * @param height      Default cell height
     * @param width       Default cell width
     * @param mode        Row height / column width adjustment mode for the cell
     * @return Actual row height or column width, determined by the mode
     */
    int prepare2ShrinkRealWidthOrHeight4Cell(CellElement cellElement,int height,int width ,int mode);
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
	<CellValueProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

Where needed, all plugin-declared cell intermediate processor interface instances are retrieved via:
`Set<CellValueProvider> providers = ExtraReportClassManager.getInstance().getArray(CellValueProvider.MARK_STRING);`

In the product, these are applied primarily within `SE`, `CellHtmlWriter`, and `ECReport` (the details of this logic involve sensitive implementation information and cannot be disclosed separately; developers only need to understand the relevant use cases and interface behavior).

## 7. Constraints and Notes

In `Object process(Object oriValue, CalculatorProvider ca)`, `oriValue` is the raw cell value as defined in the template.

In `Object processBeforeToTag(Object oriValue, CellGUIAttr guiAttr, Style style, int width, int height)`, `oriValue` is the value already transformed by `process()`. `processBeforeToTag` only affects the content displayed in standard web (non-H5) report previews. It has no effect on print or export. This method only changes what is displayed; it does not change the actual or display value of the cell itself.

In `int prepare2ShrinkRealWidthOrHeight4Cell(CellElement cellElement, int height, int width, int mode)`, `height` and `width` represent the pixel dimensions of the current cell, and `mode` indicates whether the cell is set to auto-adjust row height (1) or column width (2). Only the corresponding dimension can be modified (i.e., a single cell cannot have both its row height and column width adjusted simultaneously). At this point, the cell value is also already transformed by `process()`.

## 8. Useful Links

Demo: [demo-cell-value-provider](https://code.fanruan.com/hugh/demo-cell-value-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

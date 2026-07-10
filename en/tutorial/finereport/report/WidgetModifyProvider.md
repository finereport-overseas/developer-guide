# WidgetModifyProvider

| Property | Value |
| --- | --- |
| Module | extra-report (stable) |
| Full Class Name | `com.fr.stable.fun.WidgetModifyProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/WidgetModifyProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

When exporting, FineReport's standard product converts certain widgets to images for PDF export. `WidgetModifyProvider` is primarily used in scenarios where the product does not support exporting a particular widget type, or where the exported widget's appearance does not meet requirements, during printing or export operations.

Developers can convert widgets into images with the desired visual style for printing or export.

## 3. Interface Overview


```java
package com.fr.stable.fun;

import com.fr.form.ui.Widget;
import com.fr.report.cell.CellElement;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

import java.awt.image.BufferedImage;

/**
 * This interface is used to modify various properties of a widget
 *
 */
public interface WidgetModifyProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "WidgetModifyProvider";

    /**
     * Determines whether this provider accepts the given widget type
     *
     * @param widget Widget
     * @return Whether this provider can handle it
     */
    boolean accept(Widget widget);

    /**
     * Converts a widget to an image for export and printing
     *
     * @param ce         Current cell
     * @param pxWidth    Width in pixels
     * @param pxHeight   Height in pixels
     * @param calculator Calculator
     * @return Image converted from the widget
     */
    BufferedImage toImage(CellElement ce, int pxWidth, int pxHeight, CalculatorProvider calculator);
}


```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
        <WidgetModifyProvider class="your class name"/>
</extra-report>
```

## 6. How It Works


```java
package com.fr.write.cal;
// ...
final public class WB extends CalculatableResWorkSheet implements WriteECReport, ShrinkFitable, WBProvider {
	// ...
	/**
     * Converts widgets in the result report to images for export and printing
     *
     * @param resultbook Result report
     * @return Processed result report
     */
    public ResultECReport processWidgetAsImage(ResultECReport resultbook) {
        if (!CustomConfigManager.getInstance().isPrintWidget()) {
            return resultbook;
        }

        WB cloneWB;
        try {
            cloneWB = (WB) resultbook.clone();
        } catch (CloneNotSupportedException e) {
            cloneWB = (WB) resultbook;
        }

        Iterator it = cloneWB.cellIterator();
        DynamicUnitList rowHeightList = this.getRowHeightList_DEC();
        DynamicUnitList columnWidthList = this.getColumnWidthList_DEC();

        while (it.hasNext()) {
            BCE_WRITE ce = (BCE_WRITE) it.next();
            Widget widget = (Widget) ce.getOptionalAttributes().get(WriteCellElementAttribute.WIDGET);
            processWidgetAsImage(widget, rowHeightList, columnWidthList, ce);
        }

        return cloneWB;
    }

    private void processWidgetAsImage(Widget widget, DynamicUnitList rowHeightList,
                                      DynamicUnitList columnWidthList, BCE_WRITE ce) {
        if (widget == null || !widget.isVisible()) {
            return;
        }

        int resolution = Constants.DEFAULT_WEBWRITE_AND_SCREEN_RESOLUTION;
        // Get cell width and height
        int pxHeight = rowHeightList.getRangeValue(ce.getRow(), ce.getRow() + ce.getRowSpan()).toPixI(resolution);
        int pxWidth = columnWidthList.getRangeValue(ce.getColumn(), ce.getColumn() + ce.getColumnSpan()).toPixI(resolution);
        // Convert to image
        BufferedImage image = drawWidget(widget, ce, pxHeight, pxWidth);

        if (image != null) {
            // Widget is no longer needed after conversion; avoids redundant processing in other print steps
            ce.setValue(image);
            // If the cell also has a present value defined, clear it as well
            ce.setPresentValue(image);
        }
        ce.getOptionalAttributes().remove(WriteCellElementAttribute.WIDGET);
    }

    private BufferedImage drawWidget(Widget widget, CellElement ce, int pxHeight, int pxWidth) {
        ExtraReportClassManager extraReportClassManager = PluginModule.getAgent(PluginModule.ExtraReport);
        if (extraReportClassManager != null) {
            Set<WidgetModifyProvider> providers = extraReportClassManager.getArray(WidgetModifyProvider.MARK_STRING);
            for (WidgetModifyProvider provider : providers) {
                if (provider.accept(widget)) {
                    return provider.toImage(ce, pxWidth, pxHeight, this.getCalculator());
                }
            }
        }
        return widget.toImage(ce.getValue(), pxWidth, pxHeight, ce.getStyle(), this.getCalculator());
    }
	// ...
}
```

## 7. Constraints and Notes

As shown in the implementation above, widget export requires that `CustomConfigManager.getInstance().isPrintWidget()` is enabled. To conveniently modify this configuration, install the [Visual Configuration Plugin](https://market.fanruan.com/plugin/1052a471-0239-4cd8-b832-045d53182c5d) and set `CustomConfig.printWidget` to `true`.

If the widget is one you developed yourself, the `Widget` object already has a `toImage` method. In that case, there is no need to use `WidgetModifyProvider`—simply implement `toImage` directly in your widget class.

## 8. Useful Links

Demo: [demo-widget-modify-provider](https://code.fanruan.com/hugh/demo-widget-modify-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use is prohibited; any consequences of commercial use are solely the responsibility of the user.

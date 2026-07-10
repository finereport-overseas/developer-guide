# ChartTypeUIProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-chart-designer |
| Full Class Name | `com.fr.design.chart.fun.ChartTypeUIProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.chart.fun.ChartTypeUIProvider) |

## Overview

The designer UI interface for custom chart types. It provides a complete property configuration UI for a custom chart type, including the type-switching panel, data configuration panel, and style attribute panel. It is typically used together with `ChartTypeProvider`.

## Interface Definition

```java
/**
 * Custom chart type UI interface
 * @since 8.0
 */
public interface ChartTypeUIProvider extends Level {

    String XML_TAG = ChartXMLTag.CHART_TYPE_UI_PROVIDER;

    int CURRENT_API_LEVEL = 3;

    /**
     * Chart type definition panel (first Tab in the property sheet)
     * Return null if no type-switching panel is needed
     */
    AbstractChartTypePane getPlotTypePane();

    /**
     * Chart data configuration panel (second Tab in the property sheet)
     * Return null if no data configuration panel is needed
     */
    ChartDataPane getChartDataPane(AttributeChangeListener listener);

    /**
     * Array of additional style attribute panels (remaining Tabs in the property sheet)
     * Return an empty array if no additional style panels are needed
     */
    AbstractChartAttrPane[] getAttrPaneArray(AttributeChangeListener listener);

    /**
     * Chart type name, e.g., "Bar Chart"
     */
    String getName();

    /**
     * Array of chart sub-type names, e.g., ["Bar Chart", "Stacked Bar Chart", "100% Stacked Bar Chart"]
     */
    String[] getSubName();

    /**
     * Array of demo image paths (400×225)
     * 1. Rendered as-is in the chart selection dialog
     * 2. Rendered with scaling in the chart property type panel
     */
    String[] getDemoImagePath();

    /**
     * Small icon path (16×16), used in the form toolbar
     */
    String getIconPath();
}
```

### Deprecated Methods

The following methods are marked `@Deprecated` and do not need to be overridden in new implementations:

| Method | Description |
| --- | --- |
| `getTableDataSourcePane()` | Dataset data source panel |
| `getReportDataSourcePane()` | Cell data source panel |
| `getPlotConditionPane()` | Condition attribute panel |
| `getPlotSeriesPane()` | Series panel |
| `getChartEditPane()` | Chart edit panel |
| `getChartConfigPane()` | Chart config panel |
| `needChartChangePane()` | Whether a type-switching panel is needed |
| `isUseDefaultPane()` | Whether to use the default panel |

## Usage

Typically used together with `ChartTypeProvider`, registered to the `extra-chart-designer` module:

```xml
<extra-chart-designer>
    <ChartTypeUIProvider class="com.fr.plugin.xxx.YourChartTypeUIProvider"/>
</extra-chart-designer>
```

## Example

```java
public class MyChartTypeUIProvider implements ChartTypeUIProvider {

    @Override
    public AbstractChartTypePane getPlotTypePane() {
        // Return the chart type-switching panel; return null if no switching is needed
        return new MyChartTypePane();
    }

    @Override
    public ChartDataPane getChartDataPane(AttributeChangeListener listener) {
        // Return the data configuration panel
        return new MyChartDataPane(listener);
    }

    @Override
    public AbstractChartAttrPane[] getAttrPaneArray(AttributeChangeListener listener) {
        // Return the array of style attribute panels; return an empty array if no additional styles are needed
        return new AbstractChartAttrPane[]{
            new MyChartStylePane(listener)
        };
    }

    @Override
    public String getName() {
        return "My Custom Chart";
    }

    @Override
    public String[] getSubName() {
        return new String[]{"Basic Mode", "Advanced Mode"};
    }

    @Override
    public String[] getDemoImagePath() {
        return new String[]{
            "/com/fr/plugin/xxx/images/demo_basic.png",
            "/com/fr/plugin/xxx/images/demo_advanced.png"
        };
    }

    @Override
    public String getIconPath() {
        return "/com/fr/plugin/xxx/images/icon.png";
    }
}
```

## Notes

- The arrays returned by `getSubName()` and `getDemoImagePath()` must have the same length, corresponding to the name and preview image of each sub-type respectively.
- The recommended demo image size is 400×225; the icon size is 16×16.
- All deprecated methods have `default` implementations and do not need to be overridden. New chart plugins should use `getChartDataPane()` and `getAttrPaneArray()` instead of the old data source / condition / series methods.
- `CURRENT_API_LEVEL = 3`: ensure the `level()` return value is not lower than this when implementing.

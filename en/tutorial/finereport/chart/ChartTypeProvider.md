# ChartTypeProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-chart |
| Full Class Name | `com.fr.chart.fun.ChartTypeProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.chart.fun.ChartTypeProvider) |

## Overview

The core data interface for defining custom chart types. It defines the chart's data model, the JS/CSS resources required for front-end rendering, and the drawing entry point. It is typically used together with `ChartTypeUIProvider`, which is responsible for the designer UI.

## Interface Definition

```java
/**
 * Chart type interface
 */
public interface ChartTypeProvider extends Level {

    String XML_TAG = "ChartTypeProvider";

    int CURRENT_API_LEVEL = 3;

    /**
     * Called when the plugin is loaded (initialization)
     */
    void init();

    /**
     * Called when the plugin is unloaded (cleanup)
     */
    void destroy();

    /**
     * All sub-type chart object instances under this chart type.
     * For example, a bar chart includes: stacked bar chart, 100% stacked bar chart, etc.
     */
    ChartProvider[] getChartTypes();

    /**
     * Array of JS file paths required for rendering the chart on the web
     */
    String[] getRequiredJS();

    /**
     * Array of JS file paths required when exporting the chart (returns an empty array by default)
     */
    default String[] getRequiredExportJS() {
        return new String[0];
    }

    /**
     * Array of CSS file paths required for rendering the chart on the web
     */
    String[] getRequiredCss();

    /**
     * The name of the JS entry object used for drawing on the web
     * (typically a function that renders the chart into a specified DOM element)
     */
    String getWrapperName();
}
```

## Usage

Typically used together with `ChartTypeUIProvider`, registered to their respective modules:

```xml
<!-- Register the data model to extra-chart -->
<extra-chart>
    <ChartTypeProvider class="com.fr.plugin.xxx.YourChartTypeProvider"/>
</extra-chart>

<!-- Register the designer UI to extra-chart-designer -->
<extra-chart-designer>
    <ChartTypeUIProvider class="com.fr.plugin.xxx.YourChartTypeUIProvider"/>
</extra-chart-designer>
```

## Example

```java
public class MyChartTypeProvider implements ChartTypeProvider {

    @Override
    public void init() {
        // Initialization logic when the plugin is loaded, e.g., registering global configuration
    }

    @Override
    public void destroy() {
        // Cleanup logic when the plugin is unloaded
    }

    @Override
    public ChartProvider[] getChartTypes() {
        // Return all sub-type instances under this chart type
        return new ChartProvider[]{
            new MyBasicChart(),
            new MyStackedChart()
        };
    }

    @Override
    public String[] getRequiredJS() {
        // Return JS file paths required for web rendering (relative to the plugin resource directory)
        return new String[]{
            "/com/fr/plugin/xxx/js/mychart.js"
        };
    }

    @Override
    public String[] getRequiredCss() {
        return new String[]{
            "/com/fr/plugin/xxx/css/mychart.css"
        };
    }

    @Override
    public String getWrapperName() {
        // Return the name of the JS function/object responsible for drawing the chart
        return "MyChartWrapper";
    }
}
```

## Notes

- Each `ChartProvider` instance returned by `getChartTypes()` corresponds to one sub-type. The count and order must be consistent with `ChartTypeUIProvider.getSubName()`.
- The JS object returned by `getWrapperName()` must be defined in the files returned by `getRequiredJS()`, and must render the chart into the given DOM node.
- `init()` and `destroy()` are called when the plugin is loaded and unloaded respectively; use them to manage global resources.
- `CURRENT_API_LEVEL = 3`: ensure the `level()` return value is not lower than this when implementing.

# CellWidgetOptionProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.CellWidgetOptionProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.CellWidgetOptionProvider) |

## Overview

A sub-interface of `ParameterWidgetOptionProvider`, specifically used to add custom widget types to the cell widget selection.

The key difference from the parent interface is that `appearanceForWidget()` has a narrower return type of `BasicBeanPane<? extends Widget>`, providing stronger type constraints for the design UI.

## Interface Definition

```java
public interface CellWidgetOptionProvider extends ParameterWidgetOptionProvider {

    String XML_TAG = "CellWidgetOptionProvider";

    /**
     * The design UI class for the custom cell widget; must extend BasicBeanPane<? extends Widget>.
     */
    Class<? extends BasicBeanPane<? extends Widget>> appearanceForWidget();
}
```

Methods inherited from `ParameterWidgetOptionProvider`:

| Method | Description |
| --- | --- |
| `classForWidget()` | Returns the widget data class, which must extend `com.fr.form.ui.Widget` |
| `appearanceForWidget()` | Returns the widget design UI class, which must extend `BasicBeanPane<? extends Widget>` |
| `iconPathForWidget()` | Returns the icon path for the widget in the designer |
| `nameForWidget()` | Returns the display name of the widget |

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <CellWidgetOptionProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyCellWidgetOptionProvider implements CellWidgetOptionProvider {

    @Override
    public Class<? extends Widget> classForWidget() {
        return MyCellWidget.class;
    }

    @Override
    public Class<? extends BasicBeanPane<? extends Widget>> appearanceForWidget() {
        // Return the design UI class, extending BasicBeanPane<MyCellWidget>
        return MyCellWidgetPane.class;
    }

    @Override
    public String iconPathForWidget() {
        return "/com/fr/plugin/xxx/images/cell_widget_icon.png";
    }

    @Override
    public String nameForWidget() {
        return "My Cell Widget";
    }
}
```

## Notes

- The return type of `appearanceForWidget()` is `BasicBeanPane<? extends Widget>`, which is more strictly constrained than the parent interface `ParameterWidgetOptionProvider` (which returns `Class<?>`).
- If the Widget class returned by `classForWidget()` needs to support value binding, it must implement the `DataControl` interface; otherwise it may not be discoverable in the value editor.
- To extend form widgets (rather than cell widgets), use the `FormWidgetOptionProvider` interface instead.

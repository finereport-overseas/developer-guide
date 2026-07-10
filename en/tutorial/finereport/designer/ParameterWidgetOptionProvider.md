# ParameterWidgetOptionProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.ParameterWidgetOptionProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.ParameterWidgetOptionProvider) |

## Overview

Adds a new custom widget type to the parameter panel, cell widget, or form widget selection.

This is the base interface for parameter widget extensions. It has two sub-interfaces:
- `CellWidgetOptionProvider`: for cell widgets
- `FormWidgetOptionProvider`: for form widgets

## Interface Definition

```java
public interface ParameterWidgetOptionProvider extends Mutable {

    String XML_TAG = "ParameterWidgetOptionProvider";

    int CURRENT_LEVEL = 1;

    /**
     * The actual class for the custom parameter widget; must extend com.fr.form.ui.Widget.
     * If the widget has a value property, it must also implement the DataControl interface;
     * otherwise, it may not be discoverable in the value editor widget selector.
     */
    Class<? extends Widget> classForWidget();

    /**
     * The design UI class for the custom parameter widget;
     * must extend com.fr.form.designer.creator.XWidgetCreator.
     */
    Class<?> appearanceForWidget();

    /**
     * Icon path for the custom parameter widget in the designer.
     */
    String iconPathForWidget();

    /**
     * Display name of the custom parameter widget.
     */
    String nameForWidget();
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <ParameterWidgetOptionProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyParameterWidgetOptionProvider implements ParameterWidgetOptionProvider {

    @Override
    public Class<? extends Widget> classForWidget() {
        // Returns the custom widget data class (extends Widget)
        return MyWidget.class;
    }

    @Override
    public Class<?> appearanceForWidget() {
        // Returns the custom widget design UI class (extends XWidgetCreator)
        return MyWidgetCreator.class;
    }

    @Override
    public String iconPathForWidget() {
        // Returns the widget icon path (relative to the plugin resource directory)
        return "/com/fr/plugin/xxx/images/widget_icon.png";
    }

    @Override
    public String nameForWidget() {
        return "My Custom Widget";
    }
}
```

## Notes

- The class returned by `classForWidget()` must extend `com.fr.form.ui.Widget`.
- If the widget needs to support value binding (e.g., so it can be selected in the value editor), the corresponding Widget class must also implement the `DataControl` interface; otherwise it will not be discoverable.
- The class returned by `appearanceForWidget()` must extend `com.fr.form.designer.creator.XWidgetCreator`.
- To extend cell widgets, use the sub-interface `CellWidgetOptionProvider`; to extend form widgets, use the sub-interface `FormWidgetOptionProvider`.

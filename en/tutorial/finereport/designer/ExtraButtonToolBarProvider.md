# ExtraButtonToolBarProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.ExtraButtonToolBarProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.ExtraButtonToolBarProvider) |

## Overview

Extends the toolbar button widget's attribute configuration panel on the report designer side, allowing custom attribute settings to be added to toolbar buttons.

## Interface Definition

```java
/**
 * Designer-side extension for report toolbar, used to configure extra button attributes.
 */
public interface ExtraButtonToolBarProvider extends Mutable {

    String XML_TAG = "ExtraButtonToolBarProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Adds content to the attribute panel.
     *
     * @param centerPane attribute panel container
     */
    void updateCenterPane(JPanel centerPane);

    /**
     * Populates the attribute panel UI with widget data.
     *
     * @param widget     widget data object
     * @param card       card layout manager
     * @param centerPane attribute panel container
     */
    void populate(Widget widget, CardLayout card, JPanel centerPane);

    /**
     * Saves the attribute panel settings back to the widget.
     *
     * @param widget widget data object
     */
    void update(Widget widget);
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <ExtraButtonToolBarProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyExtraButtonToolBarProvider implements ExtraButtonToolBarProvider {

    private JCheckBox myCheckBox;

    @Override
    public void updateCenterPane(JPanel centerPane) {
        // Add custom UI components to the attribute panel
        myCheckBox = new JCheckBox("Enable custom feature");
        centerPane.add(myCheckBox);
    }

    @Override
    public void populate(Widget widget, CardLayout card, JPanel centerPane) {
        // Read attribute values from the widget and refresh the UI
        if (widget instanceof MyButtonWidget) {
            myCheckBox.setSelected(((MyButtonWidget) widget).isCustomEnabled());
        }
    }

    @Override
    public void update(Widget widget) {
        // Write UI settings back to the widget object
        if (widget instanceof MyButtonWidget) {
            ((MyButtonWidget) widget).setCustomEnabled(myCheckBox.isSelected());
        }
    }
}
```

## Notes

- The three methods correspond to the lifecycle of the attribute panel: `updateCenterPane()` initializes the panel layout, `populate()` fills in the data, and `update()` saves the data.
- `populate()` and `update()` should perform type checks on the `Widget` object to avoid exceptions when processing non-target widgets.

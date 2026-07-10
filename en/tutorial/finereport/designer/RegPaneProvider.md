# RegPaneProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.RegPaneProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.RegPaneProvider) |

## Overview

Replaces the built-in regular expression input panel (`RegFieldPane`) in the designer, allowing customization of the regex validation rule editing UI.

## Interface Definition

```java
public interface RegPaneProvider extends Immutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "RegPaneProvider";

    /**
     * Creates and returns a custom regular expression input panel.
     */
    RegFieldPane createRegPane();
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <RegPaneProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyRegPaneProvider implements RegPaneProvider {

    @Override
    public RegFieldPane createRegPane() {
        // Returns a custom regular expression input panel
        return new MyRegFieldPane();
    }
}

public class MyRegFieldPane extends RegFieldPane {

    @Override
    // Override panel initialization or validation logic
    protected void initComponents() {
        super.initComponents();
        // Add custom UI components or modify default behavior
    }
}
```

## Notes

- This interface extends `Immutable` and cannot be hot-swapped at runtime.
- `createRegPane()` should return a new instance on each call to avoid shared state causing UI conflicts.
- If multiple implementations are registered simultaneously, only the first one takes effect.

# PluginManagerProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.PluginManagerProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.PluginManagerProvider) |

## Overview

Replaces the built-in plugin management entry in the designer, typically used in OEM scenarios to customize the plugin management interface or behavior.

## Interface Definition

```java
/**
 * Replaces the plugin management entry.
 * @version 10.0
 */
public interface PluginManagerProvider extends Selectable {

    String MARK_STRING = "PluginManagerProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Returns the replacement plugin management Action.
     */
    UpdateAction pluginManagerAction();
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <PluginManagerProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyPluginManager extends AbstractPluginManagerProvider {

    @Override
    public UpdateAction pluginManagerAction() {
        return new PluginManagerActionAdapter();
    }

    @Override
    public Selector selector() {
        return new Selector() {
            @Override
            public boolean accept(ObjectHolder holder) {
                return true;
            }
        };
    }

    private static class PluginManagerActionAdapter extends PluginManagerAction {

        @Override
        public void actionPerformed(ActionEvent e) {
            // Invoke the custom plugin management dialog
            OSBasedAction osBasedAction = OSSupportCenter.getAction(PMDialogAction.class);
            osBasedAction.execute();
        }
    }
}
```

## Notes

- It is recommended to extend `AbstractPluginManagerProvider` rather than implement the interface directly, to reduce boilerplate code.
- The `selector()` method controls whether this implementation is active; returning `true` causes it to replace the default plugin management entry.
- When multiple implementations are registered simultaneously, the platform uses the `Selectable` mechanism to select the first implementation whose `accept` method returns `true`.

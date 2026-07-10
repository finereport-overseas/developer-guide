# RightSelectionHandlerProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.RightSelectionHandlerProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.RightSelectionHandlerProvider) |

## Overview

Extends the right-click context menu for cells or float elements during template creation, to meet custom functionality requirements (e.g., selective paste). Also supports adding, removing, or modifying built-in right-click options in forms and parameter panels.

## Interface Definition

```java
public interface RightSelectionHandlerProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "RightSelectionHandlerProvider";

    /**
     * Adds, removes, or modifies right-click menu items for cells or float elements.
     *
     * @param ePane     the selected element
     * @param popupMenu the right-click main menu
     */
    void dmlMenu(TargetComponent ePane, UIPopupMenu popupMenu);

    /**
     * Whether this implementation applies to the current element.
     *
     * @param selectableElement currently selected element — either CellSelection (cell) or FloatSelection (float element)
     */
    boolean accept(SelectableElement selectableElement);

    /**
     * Adds, removes, or modifies built-in right-click actions in forms or parameter panels.
     *
     * @param actions default action list; note that actions in this list must be subclasses of UndoableAction
     */
    void dmlUpdateActions(BaseFormDesigner formDesigner, List<UpdateAction> actions);

    /**
     * Whether this implementation applies to the current form/parameter panel element.
     *
     * @param formDesigner currently selected element — either a form editor or parameter panel
     */
    boolean accept(BaseFormDesigner formDesigner);
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <RightSelectionHandlerProvider class="com.fr.plugin.designer.MyActionImpl"/>
</extra-designer>
```

## Example Code

```java
public class MyRightSelectionHandler implements RightSelectionHandlerProvider {

    @Override
    public void dmlMenu(TargetComponent ePane, UIPopupMenu popupMenu) {
        // Append a custom menu item at the end of the right-click menu
        UIMenuItem myItem = new UIMenuItem("Custom Action");
        myItem.addActionListener(e -> {
            // Execute custom logic
        });
        popupMenu.add(myItem);
    }

    @Override
    public boolean accept(SelectableElement selectableElement) {
        // Only applies to cell selections
        return selectableElement instanceof CellSelection;
    }

    @Override
    public void dmlUpdateActions(BaseFormDesigner formDesigner, List<UpdateAction> actions) {
        // Do not handle form right-click menu
    }

    @Override
    public boolean accept(BaseFormDesigner formDesigner) {
        return false;
    }
}
```

## Reference

Official example source: [design-right-selection](http://git.fanruan.com/fanruan/design-right-selection)

## Notes

- `dmlMenu()` applies to right-click menu extensions for report cells and float elements.
- `dmlUpdateActions()` applies to right-click menu extensions for forms and parameter panels. Note that the action elements passed in the list must be subclasses of `UndoableAction`, not `UpdateAction`.
- The two pairs of `accept()` methods control the effective scope for different scenarios. Returning `true` in only one of them is perfectly valid.

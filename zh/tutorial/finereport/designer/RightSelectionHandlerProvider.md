# RightSelectionHandlerProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.RightSelectionHandlerProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.RightSelectionHandlerProvider) |

## 简介

在模板制作过程中，扩展单元格或悬浮元素的右键菜单，满足自定义功能需求（如选择性粘贴等）。同时也支持对表单、参数面板内置的右键选项进行增删改。

## 接口定义

```java
public interface RightSelectionHandlerProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "RightSelectionHandlerProvider";

    /**
     * 对单元格或悬浮元素的右键菜单项进行增删改
     *
     * @param ePane     选择的元素
     * @param popupMenu 右键主菜单
     */
    void dmlMenu(TargetComponent ePane, UIPopupMenu popupMenu);

    /**
     * 当前实现是否可以作用于当前元素
     *
     * @param selectableElement 当前选中元素，分为 CellSelection（单元格）和 FloatSelection（悬浮元素）
     */
    boolean accept(SelectableElement selectableElement);

    /**
     * 对表单、参数面板内置的右键选项进行增删改
     *
     * @param actions 默认的 action 集合，注意其中的 action 必须是 UndoableAction 的子类
     */
    void dmlUpdateActions(BaseFormDesigner formDesigner, List<UpdateAction> actions);

    /**
     * 当前实现是否可以作用于当前表单/参数面板元素
     *
     * @param formDesigner 当前选中元素，分为表单编辑器和参数面板
     */
    boolean accept(BaseFormDesigner formDesigner);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <RightSelectionHandlerProvider class="com.fr.plugin.designer.MyActionImpl"/>
</extra-designer>
```

## 示例代码

```java
public class MyRightSelectionHandler implements RightSelectionHandlerProvider {

    @Override
    public void dmlMenu(TargetComponent ePane, UIPopupMenu popupMenu) {
        // 在右键菜单末尾追加自定义菜单项
        UIMenuItem myItem = new UIMenuItem("自定义操作");
        myItem.addActionListener(e -> {
            // 执行自定义逻辑
        });
        popupMenu.add(myItem);
    }

    @Override
    public boolean accept(SelectableElement selectableElement) {
        // 仅对单元格选区生效
        return selectableElement instanceof CellSelection;
    }

    @Override
    public void dmlUpdateActions(BaseFormDesigner formDesigner, List<UpdateAction> actions) {
        // 不处理表单右键菜单
    }

    @Override
    public boolean accept(BaseFormDesigner formDesigner) {
        return false;
    }
}
```

## 参考示例

官方示例源码：[design-right-selection](http://git.fanruan.com/fanruan/design-right-selection)

## 注意事项

- `dmlMenu()` 适用于报表单元格和悬浮元素的右键菜单扩展。
- `dmlUpdateActions()` 适用于表单和参数面板的右键菜单扩展，注意其中传入的 action 列表要求元素为 `UndoableAction` 的子类，而非 `UpdateAction`。
- 两对 `accept()` 方法分别控制不同场景的生效范围，可根据需要只在其中一个场景中返回 `true`。

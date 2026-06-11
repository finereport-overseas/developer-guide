# ExtraButtonToolBarProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.ExtraButtonToolBarProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.ExtraButtonToolBarProvider) |

## 简介

在报表设计器端扩展工具栏按钮控件的额外属性配置面板，用于为工具栏按钮增加自定义属性设置界面。

## 接口定义

```java
/**
 * 报表工具栏设计器端拓展，用于配置按钮额外属性
 */
public interface ExtraButtonToolBarProvider extends Mutable {

    String XML_TAG = "ExtraButtonToolBarProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 添加属性面板内容
     *
     * @param centerPane 属性面板容器
     */
    void updateCenterPane(JPanel centerPane);

    /**
     * 用控件数据更新（填充）属性面板界面
     *
     * @param widget     控件数据对象
     * @param card       卡片布局管理器
     * @param centerPane 属性面板容器
     */
    void populate(Widget widget, CardLayout card, JPanel centerPane);

    /**
     * 将属性面板的设置保存回控件
     *
     * @param widget 控件数据对象
     */
    void update(Widget widget);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <ExtraButtonToolBarProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyExtraButtonToolBarProvider implements ExtraButtonToolBarProvider {

    private JCheckBox myCheckBox;

    @Override
    public void updateCenterPane(JPanel centerPane) {
        // 向属性面板添加自定义 UI 组件
        myCheckBox = new JCheckBox("启用自定义功能");
        centerPane.add(myCheckBox);
    }

    @Override
    public void populate(Widget widget, CardLayout card, JPanel centerPane) {
        // 从控件中读取属性值并刷新 UI
        if (widget instanceof MyButtonWidget) {
            myCheckBox.setSelected(((MyButtonWidget) widget).isCustomEnabled());
        }
    }

    @Override
    public void update(Widget widget) {
        // 将 UI 上的设置写回控件对象
        if (widget instanceof MyButtonWidget) {
            ((MyButtonWidget) widget).setCustomEnabled(myCheckBox.isSelected());
        }
    }
}
```

## 注意事项

- 三个方法对应属性面板的生命周期：`updateCenterPane()` 初始化面板布局，`populate()` 填充数据，`update()` 保存数据。
- `populate()` 和 `update()` 中操作的 `Widget` 对象应做类型检查，避免处理非目标控件时抛出异常。

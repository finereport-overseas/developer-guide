# CellWidgetOptionProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.CellWidgetOptionProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.CellWidgetOptionProvider) |

## 简介

`ParameterWidgetOptionProvider` 的子接口，专用于在单元格控件选择中增加自定义控件类型。

与父接口的区别在于 `appearanceForWidget()` 方法返回类型收窄为 `BasicBeanPane<? extends Widget>`，提供更强类型约束的设计界面。

## 接口定义

```java
public interface CellWidgetOptionProvider extends ParameterWidgetOptionProvider {

    String XML_TAG = "CellWidgetOptionProvider";

    /**
     * 自定义格子控件的设计界面类，需继承自 BasicBeanPane<? extends Widget>
     */
    Class<? extends BasicBeanPane<? extends Widget>> appearanceForWidget();
}
```

继承自 `ParameterWidgetOptionProvider` 的方法：

| 方法 | 说明 |
| --- | --- |
| `classForWidget()` | 返回控件数据类，需继承 `com.fr.form.ui.Widget` |
| `appearanceForWidget()` | 返回控件设计界面类，需继承 `BasicBeanPane<? extends Widget>` |
| `iconPathForWidget()` | 返回控件在设计器中的图标路径 |
| `nameForWidget()` | 返回控件显示名称 |

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <CellWidgetOptionProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyCellWidgetOptionProvider implements CellWidgetOptionProvider {

    @Override
    public Class<? extends Widget> classForWidget() {
        return MyCellWidget.class;
    }

    @Override
    public Class<? extends BasicBeanPane<? extends Widget>> appearanceForWidget() {
        // 返回设计界面类，继承自 BasicBeanPane<MyCellWidget>
        return MyCellWidgetPane.class;
    }

    @Override
    public String iconPathForWidget() {
        return "/com/fr/plugin/xxx/images/cell_widget_icon.png";
    }

    @Override
    public String nameForWidget() {
        return "我的单元格控件";
    }
}
```

## 注意事项

- `appearanceForWidget()` 返回类型为 `BasicBeanPane<? extends Widget>`，比父接口 `ParameterWidgetOptionProvider` 的返回类型（`Class<?>`）有更严格的约束。
- `classForWidget()` 返回的 Widget 类若需支持值绑定，需实现 `DataControl` 接口。
- 若需扩展表单控件（而非单元格控件），应使用 `FormWidgetOptionProvider` 接口。

# ParameterWidgetOptionProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.ParameterWidgetOptionProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.ParameterWidgetOptionProvider) |

## 简介

在参数界面、单元格或表单控件选择中增加一个新类型的自定义控件。

该接口是参数控件扩展的基础接口，有两个子接口：
- `CellWidgetOptionProvider`：用于单元格控件
- `FormWidgetOptionProvider`：用于表单控件

## 接口定义

```java
public interface ParameterWidgetOptionProvider extends Mutable {

    String XML_TAG = "ParameterWidgetOptionProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 自定义参数控件的实际类，需继承自 com.fr.form.ui.Widget
     * 若控件有值属性，需实现 DataControl 接口，否则值编辑器选择控件时可能无法找到插件控件
     */
    Class<? extends Widget> classForWidget();

    /**
     * 自定义参数控件的设计界面类，需继承自 com.fr.form.designer.creator.XWidgetCreator
     */
    Class<?> appearanceForWidget();

    /**
     * 自定义参数控件在设计器界面上的图标路径
     */
    String iconPathForWidget();

    /**
     * 自定义参数控件的名字
     */
    String nameForWidget();
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <ParameterWidgetOptionProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyParameterWidgetOptionProvider implements ParameterWidgetOptionProvider {

    @Override
    public Class<? extends Widget> classForWidget() {
        // 返回自定义控件的数据类（继承自 Widget）
        return MyWidget.class;
    }

    @Override
    public Class<?> appearanceForWidget() {
        // 返回自定义控件的设计界面类（继承自 XWidgetCreator）
        return MyWidgetCreator.class;
    }

    @Override
    public String iconPathForWidget() {
        // 返回控件图标路径（相对于插件资源目录）
        return "/com/fr/plugin/xxx/images/widget_icon.png";
    }

    @Override
    public String nameForWidget() {
        return "我的自定义控件";
    }
}
```

## 注意事项

- `classForWidget()` 返回的类必须继承自 `com.fr.form.ui.Widget`。
- 若控件需要支持值绑定（如值编辑器中可选择该控件），对应的 Widget 类还需实现 `DataControl` 接口，否则在值编辑器选择控件时将无法找到该插件控件。
- `appearanceForWidget()` 返回的类必须继承自 `com.fr.form.designer.creator.XWidgetCreator`。
- 如需扩展单元格控件，使用子接口 `CellWidgetOptionProvider`；如需扩展表单控件，使用子接口 `FormWidgetOptionProvider`。

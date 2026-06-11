# ChartTypeUIProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-chart-designer |
| 完整类名 | `com.fr.design.chart.fun.ChartTypeUIProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.chart.fun.ChartTypeUIProvider) |

## 简介

自定义图表类型的设计器界面接口，用于为自定义图表类型提供完整的属性配置 UI，包括类型切换面板、数据配置面板和样式属性面板。通常与 `ChartTypeProvider` 配对使用。

## 接口定义

```java
/**
 * 自定义图表类型界面接口
 * @since 8.0
 */
public interface ChartTypeUIProvider extends Level {

    String XML_TAG = ChartXMLTag.CHART_TYPE_UI_PROVIDER;

    int CURRENT_API_LEVEL = 3;

    /**
     * 图表类型定义界面（属性表第一个 Tab）
     * 返回 null 表示没有类型切换界面
     */
    AbstractChartTypePane getPlotTypePane();

    /**
     * 图表数据配置界面（属性表第二个 Tab）
     * 返回 null 表示没有数据配置界面
     */
    ChartDataPane getChartDataPane(AttributeChangeListener listener);

    /**
     * 其他样式属性面板数组（属性表其余 Tab）
     * 返回空数组表示没有其他样式界面
     */
    AbstractChartAttrPane[] getAttrPaneArray(AttributeChangeListener listener);

    /**
     * 图表类型名称，如"柱形图"
     */
    String getName();

    /**
     * 图表子类型名称数组，如["柱形图", "堆积柱形图", "百分比堆积柱形图"]
     */
    String[] getSubName();

    /**
     * Demo 图片路径数组（400×225）
     * 1. 图表选择界面原样渲染
     * 2. 图表属性类型界面缩放渲染
     */
    String[] getDemoImagePath();

    /**
     * 小图标路径（16×16），用于表单工具栏
     */
    String getIconPath();
}
```

### 已废弃方法

以下方法已标记 `@Deprecated`，新实现中无需重写：

| 方法 | 说明 |
| --- | --- |
| `getTableDataSourcePane()` | 数据集数据源界面 |
| `getReportDataSourcePane()` | 单元格数据源界面 |
| `getPlotConditionPane()` | 条件属性界面 |
| `getPlotSeriesPane()` | 系列界面 |
| `getChartEditPane()` | 图表编辑面板 |
| `getChartConfigPane()` | 图表配置面板 |
| `needChartChangePane()` | 是否需要类型切换面板 |
| `isUseDefaultPane()` | 是否使用默认界面 |

## 使用方式

通常与 `ChartTypeProvider` 配合，注册到 `extra-chart-designer` 模块：

```xml
<extra-chart-designer>
    <ChartTypeUIProvider class="com.fr.plugin.xxx.YourChartTypeUIProvider"/>
</extra-chart-designer>
```

## 示例代码

```java
public class MyChartTypeUIProvider implements ChartTypeUIProvider {

    @Override
    public AbstractChartTypePane getPlotTypePane() {
        // 返回图表类型切换面板，无需切换时可返回 null
        return new MyChartTypePane();
    }

    @Override
    public ChartDataPane getChartDataPane(AttributeChangeListener listener) {
        // 返回数据配置面板
        return new MyChartDataPane(listener);
    }

    @Override
    public AbstractChartAttrPane[] getAttrPaneArray(AttributeChangeListener listener) {
        // 返回样式属性面板数组，无额外样式时返回空数组
        return new AbstractChartAttrPane[]{
            new MyChartStylePane(listener)
        };
    }

    @Override
    public String getName() {
        return "我的自定义图表";
    }

    @Override
    public String[] getSubName() {
        return new String[]{"基础模式", "高级模式"};
    }

    @Override
    public String[] getDemoImagePath() {
        return new String[]{
            "/com/fr/plugin/xxx/images/demo_basic.png",
            "/com/fr/plugin/xxx/images/demo_advanced.png"
        };
    }

    @Override
    public String getIconPath() {
        return "/com/fr/plugin/xxx/images/icon.png";
    }
}
```

## 注意事项

- `getSubName()` 与 `getDemoImagePath()` 返回的数组长度应保持一致，分别对应每个子类型的名称和预览图。
- Demo 图片尺寸建议为 400×225，图标尺寸为 16×16。
- 已废弃方法均有 `default` 实现，不必重写；新开发的图表插件应使用 `getChartDataPane()` 和 `getAttrPaneArray()` 替代旧的数据源/条件/系列方法。
- `CURRENT_API_LEVEL = 3`，实现时需确保 `level()` 返回值不低于此值。

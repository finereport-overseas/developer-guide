# ChartTypeProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-chart |
| 完整类名 | `com.fr.chart.fun.ChartTypeProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.chart.fun.ChartTypeProvider) |

## 简介

自定义图表类型的核心数据接口，用于定义图表的数据模型、前端渲染所需的 JS/CSS 资源以及绘图入口。通常与 `ChartTypeUIProvider` 配对使用，后者负责设计器界面。

## 接口定义

```java
/**
 * 图表类型接口
 */
public interface ChartTypeProvider extends Level {

    String XML_TAG = "ChartTypeProvider";

    int CURRENT_API_LEVEL = 3;

    /**
     * 初始化（插件加载时调用）
     */
    void init();

    /**
     * 销毁（插件卸载时调用）
     */
    void destroy();

    /**
     * 该图表类型下所有子类型的图表对象实例
     * 例如柱形图包含：堆积柱形图、百分比堆积柱形图等
     */
    ChartProvider[] getChartTypes();

    /**
     * 图表在 Web 端展现时需要的 JS 文件路径数组
     */
    String[] getRequiredJS();

    /**
     * 图表导出时需要的 JS 文件路径数组（默认返回空数组）
     */
    default String[] getRequiredExportJS() {
        return new String[0];
    }

    /**
     * 图表在 Web 端展现时需要的 CSS 文件路径数组
     */
    String[] getRequiredCss();

    /**
     * Web 端绘图的 JS 入口对象名（通常是一个函数，执行后在指定 DOM 中绘制图表）
     */
    String getWrapperName();
}
```

## 使用方式

通常与 `ChartTypeUIProvider` 配合，分别注册到对应模块：

```xml
<!-- 数据模型注册到 extra-chart -->
<extra-chart>
    <ChartTypeProvider class="com.fr.plugin.xxx.YourChartTypeProvider"/>
</extra-chart>

<!-- 设计器界面注册到 extra-chart-designer -->
<extra-chart-designer>
    <ChartTypeUIProvider class="com.fr.plugin.xxx.YourChartTypeUIProvider"/>
</extra-chart-designer>
```

## 示例代码

```java
public class MyChartTypeProvider implements ChartTypeProvider {

    @Override
    public void init() {
        // 插件加载时的初始化逻辑，如注册全局配置
    }

    @Override
    public void destroy() {
        // 插件卸载时的清理逻辑
    }

    @Override
    public ChartProvider[] getChartTypes() {
        // 返回该图表类型下的所有子类型实例
        return new ChartProvider[]{
            new MyBasicChart(),
            new MyStackedChart()
        };
    }

    @Override
    public String[] getRequiredJS() {
        // 返回 Web 渲染所需的 JS 文件路径（相对于插件资源目录）
        return new String[]{
            "/com/fr/plugin/xxx/js/mychart.js"
        };
    }

    @Override
    public String[] getRequiredCss() {
        return new String[]{
            "/com/fr/plugin/xxx/css/mychart.css"
        };
    }

    @Override
    public String getWrapperName() {
        // 返回 JS 中负责绘制图表的函数/对象名
        return "MyChartWrapper";
    }
}
```

## 注意事项

- `getChartTypes()` 返回的每个 `ChartProvider` 实例对应一个子类型，数量和顺序应与 `ChartTypeUIProvider.getSubName()` 保持一致。
- `getWrapperName()` 对应的 JS 对象需要在 `getRequiredJS()` 返回的文件中定义，且须在给定 DOM 节点中完成图表绘制。
- `init()` 和 `destroy()` 会分别在插件加载和卸载时调用，可在此管理全局资源。
- `CURRENT_API_LEVEL = 3`，实现时需确保 `level()` 返回值不低于此值。

# DesignerStartOpenFileProcessor

| 属性 | 值 |
| --- | --- |
| 接口类型 | processor |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.DesignerStartOpenFileProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.DesignerStartOpenFileProcessor) |

## 简介

设置设计器启动时默认显示的模板文件，可用于定制设计器启动时的初始界面，例如启动时自动打开一个空白模板或指定模板。

## 接口定义

```java
/**
 * 指定设计器启动时默认打开的文件
 */
public interface DesignerStartOpenFileProcessor extends Immutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "DesignerStartOpenFileProcessor";

    /**
     * 返回设计器启动时需要打开的报表文件
     */
    FILE fileToShow();
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <DesignerStartOpenFileProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

以下示例实现启动时打开一个空白模板：

```java
public class DesignerStartWithEmptyFile implements DesignerStartOpenFileProcessor {

    @Override
    public FILE fileToShow() {
        // 返回一个空白模板文件（相对于模板根目录的路径）
        return new MemFILE("__empty_template__.cpt");
    }
}
```

## 参考示例

官方示例源码：[plugin-performance / dzstartemptyfile](http://cloud.finedevelop.com:2015/projects/PG/repos/plugin-performance/browse/src/com/fr/plugin/performance/dzstartemptyfile/)

## 注意事项

- `fileToShow()` 返回 `null` 时，设计器将使用默认启动行为。
- 该接口继承 `Immutable`，不支持 `Mutable` 相关的运行时替换机制。
- 同时注册多个实现时，仅第一个生效。

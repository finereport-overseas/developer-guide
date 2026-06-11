# CellTagTransformer

| 属性 | 值 |
| --- | --- |
| 接口类型 | processor |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.CellTagTransformer` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.report.fun.CellTagTransformer) |

## 简介

更改单元格输出的 HTML 标签（`Tag`），可用于增加 Tooltip 提示信息、自定义 HTML 属性等目的。

## 接口定义

```java
/**
 * 更改单元格输出成的 HTML 标记 Tag
 */
public interface CellTagTransformer extends Mutable {

    String MARK_STRING = "CellTagTransformer";

    int CURRENT_LEVEL = 1;

    /**
     * 处理单元格提示效果
     *
     * @param c        算子
     * @param tag      单元格 HTML 标签
     * @param tooltip  提示内容
     * @param rawValue 单元格的值
     * @return 处理后的 HTML 标签
     */
    Tag process(Calculator c, Tag tag, String tooltip, Object rawValue);
}
```

## 默认实现参考

平台内置的默认实现 `DefaultCellTooltipProcessor` 将 tooltip 内容设置为 HTML `title` 属性：

```java
public class DefaultCellTooltipProcessor extends AbstractCellTagTransformer {

    public Tag process(Calculator c, Tag tag, String tooltip, Object rawValue) {
        tag.attr("title", tooltip);
        return tag;
    }
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-report` 标签注册实现类：

```xml
<extra-report>
    <CellTagTransformer class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## 示例代码

以下示例在默认 tooltip 基础上额外添加自定义 HTML 属性：

```java
public class MyCellTagTransformer extends AbstractCellTagTransformer {

    @Override
    public Tag process(Calculator c, Tag tag, String tooltip, Object rawValue) {
        // 保留原有 title 属性
        tag.attr("title", tooltip);
        // 添加自定义数据属性，供前端 JS 使用
        tag.attr("data-raw-value", rawValue == null ? "" : rawValue.toString());
        return tag;
    }
}
```

## 注意事项

- 推荐继承 `AbstractCellTagTransformer` 而非直接实现接口，以减少样板代码。
- `process()` 中对 `tag` 的修改直接影响最终输出的 HTML，操作时注意避免覆盖其他插件已设置的属性。
- `rawValue` 为单元格原始值，可能为 `null`，使用时需做判空处理。

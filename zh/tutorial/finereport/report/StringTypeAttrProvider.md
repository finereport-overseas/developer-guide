# StringTypeAttrProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.StringTypeAttrProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.report.fun.StringTypeAttrProvider) |

## 简介

对报表业务中的字符串类型属性值进行动态计算转变，通常用于支持公式计算。目前仅导出 Excel、PDF 的密码属性支持通过该接口进行变更计算。

## 接口定义

```java
/**
 * 字符串解析接口
 *
 * @version 10.0
 */
public interface StringTypeAttrProvider extends Selectable {

    String MARK_STRING = "StringTypeAttrProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 对字符串属性进行转变处理（如公式计算）
     *
     * @param c    算子
     * @param attr 原始属性值
     * @return 处理后的字符串
     */
    String process(CalculatorProvider c, String attr);

    /**
     * 属性键枚举，标识当前处理的是哪个属性
     */
    enum AttrKey {
        /** 导出密码属性 */
        Password_Export,
        /** 默认 */
        Default
    }
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-report` 标签注册实现类：

```xml
<extra-report>
    <StringTypeAttrProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## 示例代码

以下示例将导出密码属性值作为公式进行动态计算：

```java
public class MyStringTypeAttrProvider implements StringTypeAttrProvider {

    @Override
    public Selector selector() {
        return new Selector() {
            @Override
            public boolean accept(ObjectHolder holder) {
                // 仅处理导出密码属性
                return holder != null
                    && AttrKey.Password_Export.equals(holder.get(AttrKey.class));
            }
        };
    }

    @Override
    public String process(CalculatorProvider c, String attr) {
        // 将属性值作为公式表达式计算，例如 attr = "${username}_pass"
        try {
            Object result = c.eval(attr);
            return result == null ? attr : result.toString();
        } catch (Exception e) {
            return attr;
        }
    }
}
```

## 注意事项

- 目前仅 `AttrKey.Password_Export`（导出密码）经过适配，其他属性键（`Default` 等）暂不生效。
- `process()` 中 `attr` 为原始配置字符串，可能是普通字符串或公式表达式，实现时需自行判断是否需要计算。
- 该接口继承 `Selectable`，需通过 `selector()` 的 `accept()` 方法控制生效范围。

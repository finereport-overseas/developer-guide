# ErrorMessageProcessor

| 属性 | 值 |
| --- | --- |
| 接口类型 | processor |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ErrorMessageProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.report.fun.ErrorMessageProcessor) |

## 简介

> **已废弃（@Deprecated）**：该接口已标记为废弃，推荐使用 [`StringTypeAttrProvider`](../report/StringTypeAttrProvider.md) 替代。

对报表中的错误消息字符串进行自定义处理，例如替换或翻译错误提示内容。

## 接口定义

```java
/**
 * 字符串解析接口，推荐使用 StringTypeAttrProvider
 * @deprecated
 */
@Deprecated
public interface ErrorMessageProcessor extends Immutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "ErrorMessageProcessor";

    /**
     * 对错误消息进行处理
     *
     * @param c            算子
     * @param errorMessage 原始错误消息
     * @return 处理后的错误消息
     */
    String process(Calculator c, String errorMessage);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-report` 标签注册实现类：

```xml
<extra-report>
    <ErrorMessageProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## 示例代码

```java
public class MyErrorMessageProcessor implements ErrorMessageProcessor {

    @Override
    public String process(Calculator c, String errorMessage) {
        // 将英文错误信息替换为中文
        if (errorMessage != null && errorMessage.contains("OutOfMemory")) {
            return "内存不足，请联系管理员";
        }
        return errorMessage;
    }
}
```

## 注意事项

- 该接口已被 `@Deprecated` 标记，新项目请使用 `StringTypeAttrProvider` 替代。
- 该接口继承 `Immutable`，运行期不可热替换。

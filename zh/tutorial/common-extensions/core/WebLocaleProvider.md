# WebLocaleProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-core |
| 完整类名 | `com.fr.stable.fun.WebLocaleProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.stable.fun.WebLocaleProvider) |

## 简介

处理自定义的 Locale 字符串解析逻辑。常规 Locale 格式为 `ko_KR`、`ja_JP` 等，但部分浏览器语言包不规范，会返回 `ko`、`ja` 这类简写形式。通过该接口可自定义将这类非标准字符串映射为正确的 `Locale` 对象。

## 接口定义

```java
public interface WebLocaleProvider extends Mutable {

    String XML_TAG = "WebLocaleProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 将 Locale 字符串解析为 Locale 对象
     *
     * @param localeStr 浏览器传入的 locale 字符串（可能为非标准格式）
     * @return 对应的 Locale 对象
     */
    Locale dispatchLocale(String localeStr);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-core` 标签注册实现类：

```xml
<extra-core>
    <WebLocaleProvider class="com.fr.plugin.xxx.YourWebLocaleProvider"/>
</extra-core>
```

## 示例代码

以下示例将浏览器传入的简写语言代码映射为完整 Locale：

```java
public class MyWebLocaleProvider implements WebLocaleProvider {

    @Override
    public Locale dispatchLocale(String localeStr) {
        if (localeStr == null) {
            return null;
        }
        switch (localeStr.toLowerCase()) {
            case "ko":
                return Locale.KOREA;
            case "ja":
                return Locale.JAPAN;
            case "zh":
                return Locale.SIMPLIFIED_CHINESE;
            default:
                return null;
        }
    }
}
```

## 注意事项

- `dispatchLocale()` 返回 `null` 时，平台将使用内置的默认解析逻辑继续处理。
- 仅需处理目标场景的特殊格式，标准格式（如 `zh_CN`、`en_US`）无需在此处理。
- 多个实现同时注册时，平台会依次调用，第一个返回非 `null` 值的结果生效。

# CharTypeVerifierProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-decision |
| 完整类名 | `com.fr.decision.fun.CharTypeVerifierProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.decision.fun.CharTypeVerifierProvider) |

## 简介

平台用户的手机号码和邮箱属性在后台有正则校验，该接口可变更校验逻辑，用于扩展手机号码或邮箱的校验规则。

目前仅支持用户的手机号码和邮箱（对应 `CharLimitType` 中的相关属性），其他 `CharLimitType` 属性需主版本后续适配。

## 接口定义

```java
public interface CharTypeVerifierProvider extends PlatformScaffoldVerifierProvider, Mutable {
    String MARK_STRING = "CharTypeVerifierProvider";

    int CURRENT_LEVEL = 1;
}

public interface PlatformScaffoldVerifierProvider {

    /**
     * 校验字符串
     *
     * @param targetStr     原始字符串
     * @param charLimitType 校验类型
     * @return 是否包含非法字符（true 表示包含非法字符）
     */
    boolean containIllegalChars(String targetStr, CharLimitType charLimitType);

    /**
     * 选择器，决定当前校验器是否处理该次校验
     *
     * @param targetStr     原始字符串
     * @param charLimitType 校验类型
     * @return 是否选择当前校验器
     */
    boolean accept(String targetStr, CharLimitType charLimitType);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-decision` 标签注册实现类：

```xml
<extra-decision>
    <CharTypeVerifierProvider class="com.fr.plugin.MyCharTypeVerifier"/>
</extra-decision>
```

## 示例代码

以下示例将手机号码校验扩展为同时支持中国大陆和香港格式：

```java
public class MyCharTypeVerifier implements CharTypeVerifierProvider {

    @Override
    public boolean accept(String targetStr, CharLimitType charLimitType) {
        // 仅处理手机号码类型的校验
        return charLimitType == CharLimitType.MOBILE;
    }

    @Override
    public boolean containIllegalChars(String targetStr, CharLimitType charLimitType) {
        // 自定义校验逻辑：支持大陆手机号（1开头11位）或香港手机号（+852开头）
        if (targetStr == null) {
            return true;
        }
        boolean isMainland = targetStr.matches("^1[3-9]\\d{9}$");
        boolean isHongKong = targetStr.matches("^\\+852[0-9]{8}$");
        // 不合法则返回 true
        return !isMainland && !isHongKong;
    }
}
```

## 注意事项

- `accept()` 返回 `true` 时，当前校验器才会生效，可通过此方法限定处理的校验类型。
- 目前仅 `CharLimitType.MOBILE`（手机号）和 `CharLimitType.EMAIL`（邮箱）经过验证，其他类型需等待主版本适配。
- 多个校验器同时注册时，平台会按注册顺序依次调用 `accept()`，第一个命中的校验器生效。

# OutputFormulaExtractorProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.schedule.extension.report.job.output.formula.extract.OutputFormulaExtractorProvider` |

## 背景与场景

该接口用于处理定时任务推送配置中的两类特殊场景：

1. **自定义表达式**：业务特定的表达式规则，超出 FineReport 标准语法范围
2. **跨 sheet 引用**：报表数据提取，如 `report0~A1` 或 `block0~A1`

标准 `OutputFormulaProvider` 无法支持上述场景时，通过该接口识别并转换特殊表达式。实际使用较少，仅在自定义业务需求确有必要时使用。

## 接口定义

```java
package com.fr.schedule.extension.report.job.output.formula.extract;

import com.fr.stable.fun.mark.Mutable;
import java.util.Map;
import java.util.regex.Pattern;

public interface OutputFormulaExtractorProvider<T extends BaseOutputAction>
        extends Mutable {

    String XML_TAG = "OutputFormulaExtractorProvider";
    int CURRENT_LEVEL = 1;

    String getActionClassName();

    void addFormulaToMap(T t, Pattern pattern, Map<String, Object> map);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <OutputFormulaExtractorProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`OutputFormulaExtractBox` 维护提取器注册表。内置实现包括：
- `EmailOutputFormulaExtractor`
- `SystemMsgOutputFormulaExtractor`
- `MobileMsgOutputFormulaExtractor`
- `SmsOutputFormulaExtractor`

`addFormulaToMap` 的典型实现是从推送动作属性（主题、正文内容）中提取匹配正则 pattern 的公式并存入 map。

## 常用链接

- Demo：[demo-output-formula-extractor-provider](https://code.fanruan.com/hugh/demo-output-formula-extractor-provider)

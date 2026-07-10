# OutputFormulaExtractorProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.schedule.extension.report.job.output.formula.extract.OutputFormulaExtractorProvider` |

## Background and Use Cases

This interface handles two special scenarios in scheduled task push configurations:

1. **Custom expressions**: Business-specific expression rules that fall outside FineReport's standard syntax
2. **Cross-sheet references**: Report data extraction such as `report0~A1` or `block0~A1`

When the standard `OutputFormulaProvider` cannot support the above scenarios, this interface identifies and converts the special expressions. It is rarely used in practice and should only be adopted when custom business requirements truly necessitate it.

## Interface Definition

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

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <OutputFormulaExtractorProvider class="your class name"/>
</extra-decision>
```

## How It Works

`OutputFormulaExtractBox` maintains an extractor registry. Built-in implementations include:
- `EmailOutputFormulaExtractor`
- `SystemMsgOutputFormulaExtractor`
- `MobileMsgOutputFormulaExtractor`
- `SmsOutputFormulaExtractor`

A typical implementation of `addFormulaToMap` extracts formulas matching the regex pattern from push action properties (such as subject and body content) and stores them in the map.

## Useful Links

- Demo: [demo-output-formula-extractor-provider](https://code.fanruan.com/hugh/demo-output-formula-extractor-provider)

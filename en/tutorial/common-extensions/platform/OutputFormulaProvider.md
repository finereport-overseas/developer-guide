# OutputFormulaProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.schedule.base.provider.OutputFormulaProvider` |

## Background and Use Cases

After the platform and report module were decoupled in version 10.0, the scheduled task attachment processor could no longer access result data for client-specific adjustments. This interface processes formula-type configuration items in push configurations after template calculation is complete, used for final push notification configuration delivery.

## Interface Definition

```java
package com.fr.schedule.base.provider;

import com.fr.stable.fun.mark.Mutable;
import java.util.List;
import java.util.Map;

public interface OutputFormulaProvider<T, V> extends Mutable {
    String XML_TAG = "OutputFormulaProvider";
    int CURRENT_LEVEL = 1;

    void dealWithFormulaParam(T var1, V var2, List<Map<String, Object>> var3)
            throws Exception;

    String getActionClassName();

    int taskType();
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
    <OutputFormulaProvider class="your class name"/>
</extra-decision>
```

## How It Works

`ScheduleFormulaBox` registers all declared `OutputFormulaProvider` instances into a map during initialization, keyed by `BaseOutputAction` class name and mapped to the corresponding provider implementation. Derived classes (`FormulaBox`, `DefaultFormulaBox`) invoke the registered providers when their respective task types execute.

## Notes

- To modify built-in push behavior, extend an existing formula class (such as `EmailFormula` or `SmsFormula`)
- To add a new push type, use `OutputFormulaProvider` as a supporting interface alongside other extension interfaces
- The return value of `getActionClassName()` must match the type of the first parameter in `dealWithFormulaParam()`

## Useful Links

- Demo: [demo-output-formula-provider](https://code.fanruan.com/hugh/demo-output-formula-provider)

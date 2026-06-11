# OutputFormulaProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.schedule.base.provider.OutputFormulaProvider` |

## 背景与场景

10.0 平台与报表模块解耦后，定时任务附件处理器无法访问结果数据进行客户端特定调整。该接口在模板计算完成后处理推送配置中的公式类配置项，用于最终的推送信息配置下发。

## 接口定义

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
    <OutputFormulaProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`ScheduleFormulaBox` 初始化时将所有声明的 `OutputFormulaProvider` 注册到 map 中，以 `BaseOutputAction` 类名为键映射到对应的 provider 实现。派生类（`FormulaBox`、`DefaultFormulaBox`）在对应任务类型执行时调用已注册的 provider。

## 注意事项

- 如需修改内置推送行为，继承已有公式类（如 `EmailFormula`、`SmsFormula`）
- 如需新增推送类型，将 `OutputFormulaProvider` 作为辅助接口配合其他扩展接口使用
- `getActionClassName()` 返回值必须与 `dealWithFormulaParam()` 第一个参数类型匹配

## 常用链接

- Demo：[demo-output-formula-provider](https://code.fanruan.com/hugh/demo-output-formula-provider)

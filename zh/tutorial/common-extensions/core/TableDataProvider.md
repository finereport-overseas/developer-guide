# TableDataProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.TableDataProvider` |

## 背景与场景

帆软报表支持带参数和表达式解析的数据查询数据集，前端参数可注入 SQL 片段，但存在 SQL 注入风险。10.0 版本虽内置了防注入功能，用户仍可能需要自定义拦截处理和数据查询监控。`TableDataProvider` 正是为满足这些需求而开放的接口。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.ParameterProvider;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

/**
 * 数据集参数预处理
 */
public interface TableDataProvider extends Mutable {

    String XML_TAG = "TableDataProvider";
    int CURRENT_LEVEL = 1;

    /**
     * SQL解析前篡改参数值
     */
    void processParametersBeforeAnalyzeSQL(ParameterProvider[] paras,
                                           CalculatorProvider calculatorProvider);

    /**
     * 数据集执行前修改参数和SQL
     */
    String processTableDataSQL(ParameterProvider[] paras, String sql,
                               CalculatorProvider calculatorProvider);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 | 接口名为 TableDataProcessor，仅支持单实例 |
| FR | 9.0 | 支持 | 接口名为 TableDataProcessor，仅支持单实例 |
| FR | 10.0 | 支持 | — |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 | 接口名为 TableDataProcessor，仅支持单实例 |
| BI | 4.0 | 支持 | 接口名为 TableDataProcessor，仅支持单实例 |
| BI | 5.1 | 支持 | — |
| BI | 5.1.2 | 支持 | — |
| BI | 5.1.3 | 支持 | — |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <TableDataProvider class="your class name"/>
</extra-core>
```

## 原理说明

在 `DBTableData` 结果计算过程中，系统通过 `getStatementSql` 进行参数和 SQL 的预处理，获取所有已注册的提供者实例并依次执行预处理操作。

**限制**：该接口仅适用于数据查询类数据集，或使用 `PluginModule` 接口读取机制的数据集。

## 常用链接

- Demo：[demo-table-data-provider](https://code.fanruan.com/hugh/demo-table-data-provider)

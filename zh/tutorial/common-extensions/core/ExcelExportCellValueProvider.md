# ExcelExportCellValueProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.ExcelExportCellValueProvider` |

## 背景与场景

该接口适用于需要对导出逻辑进行定向调整（而非全面重写）的场景，允许开发者在导出 Excel 时对导出的值进行处理。典型应用场景包括：数值格式转换（如将 10000 转换为"1万"）。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

public interface ExcelExportCellValueProvider extends Mutable {
    String XML_TAG = "ExcelExportCellValueProvider";
    int CURRENT_LEVEL = 1;

    Object getCellValue(Object cellElement, Object oriValue,
                        CalculatorProvider ca);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |
| BI | 4.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <ExcelExportCellValueProvider class="your class name"/>
</extra-core>
```

## 原理说明

该接口作用于导出处理的基础层。若上层导出自定义绕过了 `AbstractExcelExporter#evalCellValue`，开发者需手动调用该接口。多个值修改接口可能同时生效，开发者应自行判断哪些值需要转换。

## 常用链接

- Demo：[demo-excel-export-cell-value](https://code.fanruan.com/hugh/demo-excel-export-cell-value)

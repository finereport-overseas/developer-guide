# PrintCellValueProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.PrintCellValueProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/PrintCellValueProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

PrintCellValueProvider接口主要用于报表打印时，对单元格的结果值进行修改的场景。常见于特殊富文本转图片，以图片形式输出或打印去敏等场景。

## 三、接口介绍


```java
/*
 * Copyright (c) 2001-2014,FineReport Inc, All Rights Reserved.
 */

package com.fr.report.fun;

import com.fr.report.cell.cellattr.CellGUIAttr;
import com.fr.report.cell.cellattr.PageExportCellElement;
import com.fr.stable.fun.mark.Mutable;

/**
 * 打印时对特定的单元格内容做特殊处理的接口
 */
public interface PrintCellValueProvider extends Mutable {

    String MARK_STRING = "PrintCellValueProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 对单元格做特殊处理
     * @param cellElement 单元格
     * @param cellGUIAttr 单元格属性
     * @param oriValue  单元格元素
     * @param width 单元格宽度
     * @param height 单元格高度
     * @return 处理后的单元格值
     */
    Object getCellValue(PageExportCellElement cellElement, CellGUIAttr cellGUIAttr, Object oriValue, int width, int height);

}
```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
	<PrintCellValueProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

该接口在需要调用的地方通过：Set<PrintCellValueProvider> providers = ExtraReportClassManager.getInstance().getArray(PrintCellValueProvider.MARK_STRING); 获取到插件中所有申明单元格打印值变更接口实例。

产品中主要在PagePainter中被获取生效

## 七、特殊限制说明

无

## 八、常用链接

demo地址：[demo-print-cell-value-provider](https://code.fanruan.com/hugh/demo-print-cell-value-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

# CommentExcelProcessor

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.CommentExcelProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/CommentExcelProcessor) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

在导出需求的个性化处理中，并非所有的个性化都是要重新导出一种新的文件格式，或者对所有的现有导出逻辑都全部重来一次。部分用户需要的仅仅是对部分导出逻辑进行调整即可满足需求，帆软对这类场景提供了一系列的接口针对性的对导出的细分和细节进行处理。而CommentExcelProcessor接口就是其中之一，允许开发者在导出excel的时候，对导出的每个sheet进行单独的处理。

CommentExcelProcessor接口在设计时的主要用途是对excel的批注进行导入导出处理，导出批注内容或利用批注辅助导入。在实际运用的过程中，因为接口带入了整个sheet报表和，excel的sheet页，使得接口的实际用途变得更广泛一些，可以用于对导出的excel每个sheet做单独调整，比如折叠树支持导出、导出控件数据字典，设置单元格只读等等场景。

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.main.FineBook;
import com.fr.report.cell.CellElement;
import com.fr.report.elementcase.ElementCase;
import com.fr.report.report.ECReport;
import com.fr.report.report.WriteECReport;
import com.fr.stable.fun.mark.Immutable;
import com.fr.third.v2.org.apache.poi.ss.usermodel.Cell;
import com.fr.third.v2.org.apache.poi.ss.usermodel.Drawing;
import com.fr.third.v2.org.apache.poi.ss.usermodel.Sheet;

/**
 * 导入导出Excel的批注, 并以批注为基准来匹配填报导入Excel.
 * 1, 导出的时候把控件同一行前面的格子加上批注JSON格式
 * 2, 导入的时候把批注导入成单元格的Tooltip
 * 3, 匹配的时候判断Excel中的批注与cpt中的批注是否相同来插入删除行.
 *
 * Created by Administrator on 2016/8/11/0011.
 */
public interface CommentExcelProcessor  extends Immutable {

    String MARK_STRING = "CommentExcelProcessor";
    int CURRENT_LEVEL = 1;

    /**
     * 遍历报表, 将cpt中控件同一行前面的格子加上Json格式批注, 插入到Excel中
     *
     * @param cptReport 原始cpt表
     * @param excelReport excel表
     * @param patr 绘图笔
     */
    void addCellComment(Sheet excelReport, ElementCase cptReport, Drawing patr);

    /**
     * 将Excel中的单元格批注转换为cpt中的单元格提示.
     *
     * @param cell Excel中的单元格
     * @param cellElement Cpt中的单元格
     */
    void convertCommentToTooltip(CellElement cellElement, Cell cell);

    /**
     * 导入Excel匹配批注
     *
     * @param excelReport 当前Excel报表
     * @param curReport 当前Cpt报表
     * @param fineBook 当前finebook
     *
     * @return 是否匹配成功, 如果失败, 或者以后改成可配置, 那么还回来走标题匹配.
     */
    boolean importExcelMatchComment(ECReport excelReport, WriteECReport curReport, FineBook fineBook);

    /**
     * @deprecated
     */
    boolean importExcelMatchComment(ECReport excelReport, WriteECReport curReport);

}

```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 | 不支持BI的仪表板 |
| BI | 4.0 | 支持 | 不支持BI的仪表板 |
| BI | 5.1 | 支持 | 不支持BI的仪表板 |
| BI | 5.1.2 | 支持 | 不支持BI的仪表板 |
| BI | 5.1.3 | 支持 | 不支持BI的仪表板 |

## 五、插件注册


```xml
<extra-report>
        <CommentExcelProcessor class="your class name"/>
</extra-report>
```

## 六、原理说明

在excel07实际导出时都是继承的StreamExcel2007Exporter，在该类中的innerExportReport方法执行时会调用StreamExcelReportExporter执行具体的导出，在StreamExcelReportExporter的导出方法export()中会读取插件中申明的所有CommentExcelProcessor接口进行导出处理。

导入excel时，ExcelReportImporter#importExcel2BookBySheet会先读取插件中的CommentExcelProcessor接口调用importExcelMatchComment接口方法判断是否要执行导入的特殊处理（默认不执行），如果需要执行，则在Excel2007ReportImporter#traverseByRows方法中会调用CommentExcelProcessor接口的convertCommentToTooltip方法，对导入的值进行调整。

## 七、特殊限制说明

这是一个Immutable的接口，一个工程中只能生效一个，否则会产生冲突。

该接口仅对excel07的导入导出支持

导出时，只需要实现addCellComment接口方法即可

导入时，由importExcelMatchComment(ECReport excelReport, WriteECReport curReport, FineBook fineBook)和convertCommentToTooltip接口共同进行控制生效。

该接口涉及到对报表对象的各种操作，需要对报表对象结构本身有一定的了解。在demo示例中，给出了一些基本的方法，可以作为参考。更多的报表对象结构知识，会在其他章节中进行详细的介绍

## 八、常用链接

demo地址：[demo-comment-excel-processor](https://code.fanruan.com/hugh/demo-comment-excel-processor)

com.fr.report.fun.ExportOperateProvider

com.fr.report.fun.ExportExtensionProcessor

com.fr.stable.fun.ExcelExportCellValueProvider

com.fr.form.stable.FormExportProcessor

com.fr.report.fun.ExcelExportAppProvider

[com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

com.fr.report.fun.FormatActionProvider

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

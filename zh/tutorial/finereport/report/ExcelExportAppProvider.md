# ExcelExportAppProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ExcelExportAppProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ExcelExportAppProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

帆软报表产品中除了导出文件类型分类之外，对于excel的导出还进行了额外的细分。常见的有三种，分别是分页导出、原样导出、分页分sheet导出。当用户需要做一些其他的excel细分导出或者对现有的某中细分类型导出进行调整时，可以使用ExcelExportAppProvider接口处理。

该接口常用于对excel某一种特定导出类型进行调整的需求中。对于所有excel导出均涉及到的细分处理，则不建议使用这个接口。

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.io.collection.ExportCollection;
import com.fr.io.exporter.AppExporter;
import com.fr.io.exporter.ExcelExportType;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.SessionProvider;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Created by richie on 16/1/19.
 * Excel导出细分接口
 */
public interface ExcelExportAppProvider extends Mutable {

    String MARK_STRING = "ExcelExportAppProvider";

    int CURRENT_LEVEL = 1;


    /**
     * extype的值
     *
     * @return Excel导出的具体方式
     */
    String exportType();

    /**
     * 大数据量Excel导出时的操作方式
     *
     * @param req            HTTP请求
     * @param res            HTTP响应
     * @param sessionIDInfor 报表的会话信息
     * @param fileName       文件名
     * @param type           导出类型
     * @return 操作方式
     */
    ExportCollection newLargeDataExportCollection(HttpServletRequest req, HttpServletResponse res,
                                                  SessionProvider sessionIDInfor,
                                                  String fileName, ExcelExportType type);

    /**
     * 导出器
     *
     * @param collection     操作方式
     * @param exportType     导出类型
     * @param sessionIDInfor 报表的会话信息
     * @return Excel导出器
     */
    AppExporter<Boolean> newAppExporter(ExportCollection collection, ExcelExportType exportType, SessionProvider sessionIDInfor);
}

```

ExportCollection介绍

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
        <ExcelExportAppProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

在excel导出操作器ExcelOperate被调用时。操作器接口newExportCollection经由createExcelExportCollection调用，一共有两个导出集合封装的方法createExcelExportCollection和createLargeDataExportCollection。在这两个方法中会读取插件中申明的所有excel细分接口实现并生效。

## 七、特殊限制说明

这是一个Mutable的接口，可以在同一次excel导出中生效多个接口实例。

该接口一共3个接口方法，其中必须实现的方法只有exportType。用于指定该接口需要处理的excel导出细分类型。细分类型可以是产品已有的（产品已有的类型见下表），也可以是自己新定义的。如果是返回产品已有的类型，则代表覆盖产品本身的excel细分导处类型。

| 类型 | 参数标志 | 备注 |
| --- | --- | --- |
| 分页 | ExportConstants.TYPE_PAGE |  |
| 原样 | ExportConstants.TYPE_SIMPLE |  |
| 分页分sheet | ExportConstants.TYPE_PAGETOSHETT |  |
| 大数据 | ExportConstants.TYPE_LARGEDATA_PAGE |  |

newLargeDataExportCollection接口是专用于大数据量导出excel细分类型的接口。其他产品已有excel导出细分类型和自定义的类型则不用实现这个接口。

## 八、常用链接

demo地址：[demo-excel-export-app-provider](https://code.fanruan.com/hugh/demo-excel-export-app-provider)

com.fr.report.fun.ExportOperateProvider

com.fr.report.fun.ExportExtensionProcessor

com.fr.stable.fun.ExcelExportCellValueProvider

com.fr.form.stable.FormExportProcessor

com.fr.report.fun.CommentExcelProcessor

[com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

com.fr.report.fun.FormatActionProvider

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

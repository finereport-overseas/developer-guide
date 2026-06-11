# PDFExporterCreator

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.io.exporter.PDFExporterCreator` |

## 背景与场景

帆软报表产品在 PDF 导出场景上有细粒度的划分，常见场景包括：普通导出、打印预览和自动打印。当需要自定义 PDF 导出类型，或对已有导出类型进行调整时，可使用 `PDFExporterCreator` 接口。该接口适用于针对特定导出类型进行定向调整，而非对全部 PDF 导出进行统一处理。

## 接口定义

```java
package com.fr.io.exporter;

import com.fr.stable.fun.mark.Immutable;

public interface PDFExporterCreator extends Immutable {
    String MARK_STRING = "PDFExporterCreator";
    int CURRENT_LEVEL = 1;

    @Deprecated
    PDFExporterProcessor createPDFExporter(boolean isPrint);

    /**
     * PDF 导出
     * @param exportType 导出类型：普通、打印预览、自动打印
     */
    PDFExporterProcessor createPDFExporter(PDFExportType exportType);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 | — |
| FR | 9.0 | 支持 | — |
| FR | 10.0 | 支持 | — |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 | 不支持仪表板 |
| BI | 4.0 | 支持 | 不支持仪表板 |
| BI | 5.1 | 支持 | 不支持仪表板 |
| BI | 5.1.2 | 支持 | 不支持仪表板 |
| BI | 5.1.3 | 支持 | 不支持仪表板 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <PDFExporterCreator class="your class name"/>
</extra-core>
```

## 原理说明

所有 PDF 导出均通过 `PDFExporterFactory` 完成实例创建。`PDFExporterFactory` 加载时，会读取插件声明的 `PDFExporterCreator` 实例。执行 `PDFExporterFactory#getPDFExporter` 时，会调用已加载的 `PDFExporterCreator` 实例生成具体的导出实例。

该接口为 `Immutable`（排他性），环境中只会有一个插件实现生效，请确认环境中不存在冲突插件。`PDFExporterProcessor` 仅是对 `AppExporter` 的抽象包装，建议直接继承 `PDFExporter`、`PDFExporterForPrint` 或 `PDFEmbExporter`。

## 常用链接

- Demo：[demo-pdf-exporter-creator](https://code.fanruan.com/hugh/demo-pdf-exporter-creator)

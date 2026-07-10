# ExcelExportAppProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ExcelExportAppProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ExcelExportAppProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

In addition to categorizing export file types, FineReport also provides further sub-categories for Excel exports. The three common Excel export modes are: paginated export, as-is export, and paginated-to-sheet export. When a user needs to add another Excel export sub-category or adjust the behavior of an existing one, the `ExcelExportAppProvider` interface can be used.

This interface is typically used to adjust one specific Excel export type. It is not recommended for use when the customization applies to all Excel export types.

## 3. Interface Overview


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
 * Excel export sub-type interface
 */
public interface ExcelExportAppProvider extends Mutable {

    String MARK_STRING = "ExcelExportAppProvider";

    int CURRENT_LEVEL = 1;


    /**
     * The extype value
     *
     * @return Specific Excel export mode
     */
    String exportType();

    /**
     * Export collection for large-data Excel exports
     *
     * @param req            HTTP request
     * @param res            HTTP response
     * @param sessionIDInfor Report session information
     * @param fileName       File name
     * @param type           Export type
     * @return Export collection
     */
    ExportCollection newLargeDataExportCollection(HttpServletRequest req, HttpServletResponse res,
                                                  SessionProvider sessionIDInfor,
                                                  String fileName, ExcelExportType type);

    /**
     * Exporter
     *
     * @param collection     Export collection
     * @param exportType     Export type
     * @param sessionIDInfor Report session information
     * @return Excel exporter
     */
    AppExporter<Boolean> newAppExporter(ExportCollection collection, ExcelExportType exportType, SessionProvider sessionIDInfor);
}

```

ExportCollection reference

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |
| BI | 3.6 | Yes | Does not support BI dashboards |
| BI | 4.0 | Yes | Does not support BI dashboards |
| BI | 5.1 | Yes | Does not support BI dashboards |
| BI | 5.1.2 | Yes | Does not support BI dashboards |
| BI | 5.1.3 | Yes | Does not support BI dashboards |

## 5. Plugin Registration


```xml
<extra-report>
        <ExcelExportAppProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

When the Excel export operator `ExcelOperate` is invoked, its `newExportCollection` method is called through `createExcelExportCollection`. There are two export collection creation methods: `createExcelExportCollection` and `createLargeDataExportCollection`. Both methods read all plugin-declared Excel sub-type interface implementations and apply them.

## 7. Constraints and Notes

This is a `Mutable` interface, so multiple implementations can be active during a single Excel export.

Of the three interface methods, only `exportType()` is required. It specifies which Excel export sub-type this interface handles. The sub-type can be one of the existing built-in types (see the table below) or a newly defined custom type. Returning a built-in type overrides the corresponding built-in Excel export sub-type.

| Type | Parameter Flag | Notes |
| --- | --- | --- |
| Paginated | ExportConstants.TYPE_PAGE |  |
| As-is | ExportConstants.TYPE_SIMPLE |  |
| Paginated to sheet | ExportConstants.TYPE_PAGETOSHETT |  |
| Large data | ExportConstants.TYPE_LARGEDATA_PAGE |  |

`newLargeDataExportCollection` is exclusively for the large-data Excel export sub-type. It does not need to be implemented for other built-in or custom Excel export sub-types.

## 8. Useful Links

Demo: [demo-excel-export-app-provider](https://code.fanruan.com/hugh/demo-excel-export-app-provider)

Related interfaces:
- `com.fr.report.fun.ExportOperateProvider`
- `com.fr.report.fun.ExportExtensionProcessor`
- `com.fr.stable.fun.ExcelExportCellValueProvider`
- `com.fr.form.stable.FormExportProcessor`
- `com.fr.report.fun.CommentExcelProcessor`
- [com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)
- `com.fr.report.fun.FormatActionProvider`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

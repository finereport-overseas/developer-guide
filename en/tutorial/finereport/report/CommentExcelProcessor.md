# CommentExcelProcessor

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.CommentExcelProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/CommentExcelProcessor) |

---

## 1. Terminology

None

## 2. Background and Use Cases

When implementing custom export logic, not every customization requires exporting a brand-new file format or completely reimplementing all existing export logic. Some users only need to adjust specific parts of the export process. FineReport provides a series of interfaces for such targeted, fine-grained export control, and `CommentExcelProcessor` is one of them. It allows developers to process each individual sheet when exporting to Excel.

The primary intended use of `CommentExcelProcessor` is to handle the import/export of Excel cell comments — exporting comment content or using comments to assist with data import matching. In practice, because the interface provides access to the entire report sheet and Excel sheet, it has become useful for a wider range of scenarios: for example, supporting collapsible tree export, exporting widget data dictionaries, setting cells as read-only, and so on.

## 3. Interface Overview


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
 * Imports and exports Excel comments, and uses comments as a basis for matching
 * fill-in report imports from Excel.
 * 1. On export: adds JSON-formatted comments to the cells preceding widget cells in the same row.
 * 2. On import: converts comments into cell tooltips.
 * 3. On matching: compares comments in the Excel file with those in the cpt template to determine
 *    row insertion or deletion.
 *
 * Created by Administrator on 2016/8/11/0011.
 */
public interface CommentExcelProcessor  extends Immutable {

    String MARK_STRING = "CommentExcelProcessor";
    int CURRENT_LEVEL = 1;

    /**
     * Iterates over the report and adds JSON-formatted comments to the cells
     * preceding widget cells in the same row, inserting them into the Excel sheet.
     *
     * @param cptReport   Original cpt template
     * @param excelReport Excel sheet
     * @param patr        Drawing patriarch
     */
    void addCellComment(Sheet excelReport, ElementCase cptReport, Drawing patr);

    /**
     * Converts Excel cell comments into cell tooltips in the cpt template.
     *
     * @param cell        Excel cell
     * @param cellElement Cpt cell element
     */
    void convertCommentToTooltip(CellElement cellElement, Cell cell);

    /**
     * Matches comments when importing an Excel file.
     *
     * @param excelReport Current Excel report
     * @param curReport   Current cpt report
     * @param fineBook    Current FineBook
     *
     * @return Whether the match succeeded. If it fails, the system falls back to header-based matching.
     */
    boolean importExcelMatchComment(ECReport excelReport, WriteECReport curReport, FineBook fineBook);

    /**
     * @deprecated
     */
    boolean importExcelMatchComment(ECReport excelReport, WriteECReport curReport);

}

```

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
        <CommentExcelProcessor class="your class name"/>
</extra-report>
```

## 6. How It Works

All Excel 07 exports extend `StreamExcel2007Exporter`. When its `innerExportReport` method runs, it delegates to `StreamExcelReportExporter` for the actual export. Within `StreamExcelReportExporter`'s `export()` method, all plugin-declared `CommentExcelProcessor` implementations are retrieved and applied.

On Excel import, `ExcelReportImporter#importExcel2BookBySheet` first retrieves the plugin's `CommentExcelProcessor` and calls `importExcelMatchComment` to determine whether special import handling is needed (not executed by default). If it is needed, `Excel2007ReportImporter#traverseByRows` calls `CommentExcelProcessor#convertCommentToTooltip` to adjust the imported values.

## 7. Constraints and Notes

This is an `Immutable` interface, meaning only one implementation can be active at a time. Multiple implementations will conflict.

This interface only supports Excel 07 import and export.

For export, only the `addCellComment` method needs to be implemented.

For import, `importExcelMatchComment(ECReport excelReport, WriteECReport curReport, FineBook fineBook)` and `convertCommentToTooltip` work together to control import behavior.

This interface involves various operations on report objects, and requires some familiarity with the report object model. The demo provides some basic usage examples as a reference. A more detailed introduction to report object structures will be covered in other chapters.

## 8. Useful Links

Demo: [demo-comment-excel-processor](https://code.fanruan.com/hugh/demo-comment-excel-processor)

Related interfaces:
- `com.fr.report.fun.ExportOperateProvider`
- `com.fr.report.fun.ExportExtensionProcessor`
- `com.fr.stable.fun.ExcelExportCellValueProvider`
- `com.fr.form.stable.FormExportProcessor`
- `com.fr.report.fun.ExcelExportAppProvider`
- [com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)
- `com.fr.report.fun.FormatActionProvider`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

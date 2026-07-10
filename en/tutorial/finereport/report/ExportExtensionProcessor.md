# ExportExtensionProcessor

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ExportExtensionProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ExportExtensionProcessor) |

---

## 1. Terminology

None

## 2. Background and Use Cases

For handling a single export type, `ExportOperateProvider` is typically used. However, when a common operation needs to apply to all report exports—such as pushing notifications for all exports, encrypting exported files, or creating backups—a more unified interface is required. FineReport provides `ExportExtensionProcessor` for this purpose. This interface can intercept and process all cpt-type report export scenarios in a unified manner. Because the interface was introduced early, when Decision Reports did not yet support exporting, it only handles cpt report exports.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.io.collection.ExportCollection;
import com.fr.stable.fun.mark.Immutable;
import com.fr.web.core.ReportSessionIDInfor;
import com.fr.web.core.TemplateSessionIDInfo;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Custom export interface
 */
public interface ExportExtensionProcessor extends Immutable{

    String MARK_TAG = "ExportExtensionProcessor";

    int CURRENT_LEVEL = 1;


    /**
     * File name for the exported file
     * @param req            HTTP request
     * @param sessionIDInfor Report session information
     * @return File name
     */
    String fileName(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor) throws Exception;

    /**
     * Export collection required for the export
     * @param req            HTTP request
     * @param res            HTTP response
     * @param sessionIDInfor Report session information
     * @param format         Export format
     * @param fileName       Export file name
     * @param isEmbed        Whether this is an embedded export
     * @return Export operation collection
     * @throws Exception Thrown if an exception occurs during export
     */
    ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res,
                                      ReportSessionIDInfor sessionIDInfor, String format,
                                      String fileName, boolean isEmbed) throws Exception;
}

```


```java
package com.fr.web.core.reserve;

// ... imports omitted for brevity ...

/**
 * Created by richie on 15/11/30.
 */
public class DefaultExportExtension extends AbstractExportExtension {

    public String fileName(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor) throws Exception {
        // Allow renaming; fall back to report name if not provided
        String fileName = NetworkHelper.getHTTPRequestFileNameParameter(req);

        // Used for op=fs programmatic report
        if (fileName == null) {
            fileName = (String) sessionIDInfor.getParameterValue(ParameterConstants.__FILENAME__);
        }
        if (fileName == null) {
            // Use the web title of the report being exported
            fileName = sessionIDInfor.getWebTitle().replaceAll("\\s", "_");
            // Export file names cannot contain commas, otherwise an error is thrown
            fileName = fileName.replaceAll(",", "_");
        }

        Browser browser = Browser.resolve(req);
        fileName = browser.getEncodedFileName4Download(fileName);
        return fileName;
    }

    public ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res,
                                                   ReportSessionIDInfor sessionIDInfor, String format,
                                                   String fileName, boolean isEmbed) throws Exception {
        Operate operate = ExportFactory.getOperate(format.toLowerCase());
        if (operate != null) {
            operate.setContent(req, res, sessionIDInfor, fileName, isEmbed);
            return operate.newExportCollection(req, res, sessionIDInfor, fileName);
        }
        return ExportCollection.create();
    }
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
        <ExportExtensionProcessor class="your class name"/>
</extra-report>
```

## 6. How It Works

In the legacy report web service framework, version 10.0 includes an `ExportService` (op=export) for handling report export operations. In the `dealWithExport` method, it retrieves the currently active export extension declaration (`ExportExtension`) and performs the corresponding export operation.

## 7. Constraints and Notes

This interface is `Immutable`—it is exclusive. If multiple plugins declare it, only one will take effect. If no plugin declares it, the default `DefaultExportExtension` is used (which can be referenced as a development reference).

Because this interface sits at the top-level entry point of all exports, it will affect all other export-related interfaces. Use it with extreme caution. Unless there is a special requirement, developers should avoid this interface as it can easily conflict with other plugins. **Note: New marketplace plugins are strictly prohibited from using this interface, to prevent widespread conflicts.**

This interface only applies to cpt reports.

## 8. Useful Links

Demo: [demo-export-extension-processor](https://code.fanruan.com/hugh/demo-export-extension-processor)

Related interfaces:
- `com.fr.report.fun.ExportOperateProvider`
- `com.fr.stable.fun.ExcelExportCellValueProvider`
- `com.fr.form.stable.FormExportProcessor`
- `com.fr.report.fun.ExcelExportAppProvider`
- `com.fr.report.fun.CommentExcelProcessor`
- [com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)
- `com.fr.report.fun.FormatActionProvider`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

# FormatActionProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.FormatActionProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/FormatActionProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

FineReport has several export-related interfaces. `FormatActionProvider` is a special one that works similarly to a filter: it directly replaces the handling of an export request, allowing developers to intervene in the export logic.

This interface only takes effect in direct-export scenarios (using `viewlet=xxx.cpt&format=xxx`, without previewing first).

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * For special format operations in reports (as opposed to standard file exports),
 * such as direct form submission and JS-based PDF printing.
 * Created by zack on 2016/3/3.
 */
public interface FormatActionProvider extends Mutable{

    String XML_TAG = "FormatActionProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Executes the request
     * @param req Request
     * @param res Response
     * @throws Exception Exception
     */
    void doAction(HttpServletRequest req, HttpServletResponse res) throws Exception;

    /**
     * Defines the key corresponding to this action, similar to cmd; defaults to cmd
     * @return key
     */
    String getCMD();
}


```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |
| BI | 3.6 | Yes | Does not support dashboards |
| BI | 4.0 | Yes | Does not support dashboards |
| BI | 5.1 | Yes | Does not support dashboards |
| BI | 5.1.2 | Yes | Does not support dashboards |
| BI | 5.1.3 | Yes | Does not support dashboards |

## 5. Plugin Registration


```xml
<extra-report>
        <FormatActionProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

The registration and activation of this interface are straightforward, as illustrated by the following code snippets.


```java
package com.fr.web.core.reserve;

// ... imports omitted for brevity ...

/**
 * Created by zack on 2016/3/3.
 */
public class FormatActionFactory {
    private static Map<String, Class<? extends FormatActionProvider>> detailMap = new HashMap<String, Class<? extends FormatActionProvider>>();

    static {
        detailMap.put("pt_print", PDFPrintPrintAction.class); // PDF printing
        Set<FormatActionProvider> formatActionProviders = ExtraReportClassManager.getInstance().getArray(FormatActionProvider.XML_TAG);
        for (FormatActionProvider provider : formatActionProviders) {
            detailMap.put(provider.getCMD(), provider.getClass());
        }
    }

    public static void registerActionProvider(String name, Class<? extends FormatActionProvider> clazz) {
        detailMap.put(name, clazz);
    }

    public static FormatActionProvider getReqProcessor(String format) {
        Class<? extends FormatActionProvider> c = detailMap.get(format);
        try {
            if (c != null) {
                return c.newInstance();
            }
        } catch (InstantiationException e) {
            FineLoggerFactory.getLogger().error(e.getMessage(), e);
        } catch (IllegalAccessException e) {
            FineLoggerFactory.getLogger().error(e.getMessage(), e);
        }
        return null;
    }
}


```


```java
package com.fr.web.core.reserve;

// ...

public class ReportletDealWith {
    // ...
    /**
     * Processes the template
     *
     * @param req    HTTP request
     * @param res    HTTP response
     * @param weblet Template
     * @throws Exception e
     */
    public static void dealWithReportlet(HttpServletRequest req, HttpServletResponse res, Weblet weblet) throws Exception {
        String sessionID = SessionPoolManager.generateSessionID(req, res, weblet);

        // ...

        if (WebUtils.getHTTPRequestParameter(req, "format") != null) {
            turnToExportWithSessionKept(req, res, sessionID);
        } else {
            dealWithReportlet(req, res, sessionID, null);
        }
    }


    public static void turnToExportWithSessionKept(HttpServletRequest req, HttpServletResponse res, String sessionID) throws Exception {
        ExportSessionKeeper.getInstance().keepAlive(sessionID);
        turnToExport(req, res, sessionID);
        ExportSessionKeeper.getInstance().close(sessionID);
    }


    /**
     * Redirects to export
     *
     * @param req       request
     * @param res       response
     * @param sessionID session id
     * @throws Exception e
     */
    public static void turnToExport(HttpServletRequest req, HttpServletResponse res, String sessionID) throws Exception {
        // Check if the output needs to be in another form, e.g. PDF, PDF_Activex, Excel, etc.
        String format = WebUtils.getHTTPRequestParameter(req, "format");
        FormatActionProvider requestProcessor = FormatActionFactory.getReqProcessor(format);
        if (requestProcessor != null) {
            requestProcessor.doAction(req, res);
        } else {
            String embedParameter = WebUtils.getHTTPRequestParameter(req, ParameterConstants.EXPORT_PDF_EMBED);
            boolean embed = "true".equals(embedParameter); // Only embed when the parameter is explicitly "true"
            ExportService.dealWithExport(req, res, sessionID, embed);
        }
    }

    // ...
}

```

## 7. Constraints and Notes

`FormatActionProvider` is a very open interface that gives developers a lot of freedom. However, precisely because it is so open, it is prone to instability and conflicts. If not carefully designed, it can easily cause all other export interfaces to stop working. Use this interface with extreme caution and it is not recommended for marketplace plugins.

In the product, besides standard exports, PDF printing (`PDFPrintPrintAction`, format=pt_print) and auto-submit (`WriteAutoSubmitAction`, format=submit) both use this interface. Developers can use it to replace these two actions for specific requirements.

As shown in `FormatActionFactory`, `FormatActionProvider` can be registered not only via `plugin.xml` but also programmatically by calling `FormatActionFactory.registerActionProvider("mycmd", MyAction.class)`. Developers can choose the approach that best fits their needs.

Although `FormatActionProvider` is `Mutable`, at most one implementation takes effect per export call. Later-registered implementations override earlier ones for the same format.

**⚠️ Note: When replacing an existing action's logic, be sure to understand how the original logic works and whether any other interfaces depend on it. If so, you must ensure compatibility in your implementation, or coordinate with users to clarify how conflicts should be avoided.**

## 8. Useful Links

Demo: [demo-format-action-provider](https://code.fanruan.com/hugh/demo-format-action-provider)

Related interfaces:
- `com.fr.report.fun.ExportOperateProvider`
- `com.fr.report.fun.ExportExtensionProcessor`
- `com.fr.stable.fun.ExcelExportCellValueProvider`
- `com.fr.form.stable.FormExportProcessor`
- `com.fr.report.fun.ExcelExportAppProvider`
- `com.fr.report.fun.CommentExcelProcessor`
- [com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

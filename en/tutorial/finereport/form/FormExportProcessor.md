# FormExportProcessor

| Property | Value |
| --- | --- |
| Module | extra-form |
| Full Class Name | `com.fr.form.stable.FormExportProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/FormExportProcessor) |

---

## I. Special Terms

None

## II. Background and Use Case

As decision-making reports (dashboards) are increasingly used in real-world projects, they have long lacked export support. Some users find it time-consuming to recreate dashboard content as a `.cpt` report just to enable export, and there has been growing demand for native export support in dashboards. The standard product only supports exporting the report block content within a dashboard. To provide richer extensibility, the official SDK exposes the `FormExportProcessor` interface for handling dashboard export needs. However, this interface is not yet fully mature — it has many limitations and is not entirely stable. At this stage, it is primarily suited for simple dashboard export scenarios.

## III. Interface Definition

```java
package com.fr.form.stable;

import com.fr.stable.fun.mark.Immutable;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Form export interface. Exports to Excel, PDF, etc. by passing different format parameters.
 *
 * Created by Administrator on 2015/12/18 0018.
 */
public interface FormExportProcessor extends Immutable {

    String MARK_STRING = "FormExportProcessor";

    int CURRENT_LEVEL = 3;

    /**
     * Perform the export
     *
     * @param req       HTTP request
     * @param res       HTTP response
     * @param sessionID Session ID
     * @throws Exception
     */
    void dealWithExport(HttpServletRequest req, HttpServletResponse res, String sessionID);

}
```

## IV. Supported Versions

| Product Line | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported |  |
| FR | 9.0 | Supported |  |
| FR | 10.0 | Supported |  |
| FR | 11.0 | Supported | |
| BI | 3.6 | Supported | BI dashboards not supported |
| BI | 4.0 | Supported | BI dashboards not supported |
| BI | 5.1 | Supported | BI dashboards not supported |
| BI | 5.1.2 | Supported | BI dashboards not supported |
| BI | 5.1.3 | Supported | BI dashboards not supported |

## V. Plugin Registration

```xml
<extra-form>
        <FormExportProcessor class="your class name"/>
</extra-form>
```

## VI. How It Works

When a dashboard is previewed, the back-end invokes `FormletDealWith#dealWithFormlet` via `Formlet#dealWeblet` to compute and respond. Inside `dealWithFormlet`, if the request contains a `format` parameter, it branches into the export handler, retrieves the dashboard export class declared by the plugin, and executes the response.

## VII. Limitations

This interface is `Immutable`, meaning it is exclusive — if multiple plugins use it, only one will take effect. If no plugin implements this interface, the standard product does not support any form of dashboard export by default.

When implementing the export, developers can use the `sessionID` parameter to obtain the dashboard result object and read the layout and component information to produce the desired export output. [[See Example](https://code.fanruan.com/hugh/demo-form-export-processor/src/branch/10.0/src/main/java/com/tptj/demo/hg/form/export/processor/Demo.java)]

This interface only applies to dashboards (decision-making reports).

> ⚠️ Note: This interface has a narrow use case, and developing a fully functional export feature is quite complex — it is tightly coupled to the product and generally unstable. It is not recommended for most developers. Export scenarios should be implemented using `.cpt` reports whenever possible. Otherwise, as development deepens, you will encounter many difficult issues (adaptation, compatibility, layout, window clipping, etc.).

## VIII. Useful Links

Demo: [demo-form-export-processor](https://code.fanruan.com/hugh/demo-form-export-processor)

Related interfaces: ExportOperateProvider, ExportExtensionProcessor, ExcelExportAppProvider, ExcelExportCellValueProvider, CommentExcelProcessor

## IX. Open Source Examples

Disclaimer: All open source examples in the documentation are independently developed and provided by community developers. They are for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on any results related to these open source examples. Any commercial use is at the sole risk and responsibility of the user.

None available.

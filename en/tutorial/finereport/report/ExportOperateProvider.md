# ExportOperateProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ExportOperateProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ExportOperateProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

Export is one of the most frequently used and convenient features in FineReport. The product already supports the common file types natively. However, certain industries or scenarios have their own file standards and export specifications. FineReport exposes specific interfaces to address these needs.

There are several export-related interfaces, and `ExportOperateProvider` is primarily used to add support for entirely new file types. Usage is consistent with standard product exports—invoked by specifying the `format` parameter in the URL.

This interface is suited for single-type export scenarios. For handling multiple export types simultaneously, refer to `ExportExtensionProcessor`.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.web.core.reserve.Operate;

/**
 * Created by richie on 16/1/19.
 * Custom export interface; recommended usage is format=xxx (where xxx is a unique export type identifier)
 */
public interface ExportOperateProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "ExportOperateProvider";

    /**
     * Export preparation operation
     *
     * @return Operation object
     */
    Operate operate();

    /**
     * Export type
     *
     * @return Type
     */
    String markType();
}

```


```java
package com.fanruan.api.report.export;

import com.fr.io.collection.ExportCollection;
import com.fr.stable.web.SessionProvider;
import com.fr.web.core.reserve.Operate;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @author richie
 * @version 10.0
 * Created by richie on 2019/9/26
 * Abstract base class for the export interface
 */
public abstract class BaseOperate implements Operate {

    @Override
    public void setContent(HttpServletRequest req, HttpServletResponse res, String fileName, boolean isEmbed) {

    }

    @Override
    public void setContent(HttpServletRequest req, HttpServletResponse res, SessionProvider sessionProvider, String fileName, boolean isEmbed) {
        this.setContent(req, res, fileName, isEmbed);
    }


    /**
     * The actual export operation
     *
     * @param req             HTTP request
     * @param res             HTTP response
     * @param sessionProvider Template session information
     * @param fileName        File name
     * @return Export collection object
     */
    @Override
    public abstract ExportCollection newExportCollection(HttpServletRequest req, HttpServletResponse res, SessionProvider sessionProvider, String fileName);
}


```


```java
package com.fr.web.core.reserve;

import com.fr.io.collection.ExportCollection;
import com.fr.stable.web.SessionProvider;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Created by richie on 16/1/19.
 */
public interface Operate {

    void setContent(HttpServletRequest req, HttpServletResponse res, String fileName, boolean isEmbed);

    void setContent(HttpServletRequest req, HttpServletResponse res, SessionProvider sessionIDInfor, String fileName, boolean isEmbed);

    ExportCollection newExportCollection(HttpServletRequest req, HttpServletResponse res, SessionProvider sessionIDInfor, String fileName);
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
| BI | 3.6 | Yes |  |
| BI | 4.0 | Yes |  |
| BI | 5.1 | Yes |  |
| BI | 5.1.2 | Yes |  |
| BI | 5.1.3 | Yes |  |

## 5. Plugin Registration


```xml
<extra-report>
        <ExportOperateProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

`ExportFactory` is the factory class for all export types, dispatched by the `format` parameter. At load time, the factory registers all new export types declared by plugins into the `OPERATE_MAP<String, Operate>` object. When an export service is called, it uses the `format` parameter to retrieve the specific export operator and perform the export.

## 7. Constraints and Notes

Because plugin instances are registered into `OPERATE_MAP<String, Operate>` after the built-in export types and directly by the `format` value, this interface can also be used in special cases to replace existing export operators—simply return the same value as the target operator's type in `markType()`.

**Note: If used to replace a built-in export operator, the plugin must NOT be published to the marketplace, as this may cause conflicts with other plugins.**

During development, to ensure better compatibility, do not implement the `Operate` interface directly—extend `BaseOperate` instead.

`BaseOperate#setContent` is used to declare the file type, encoding, and other information in the response header. [[See example](https://code.fanruan.com/hugh/demo-export-operate-provider/src/branch/10.0/src/main/java/com/tptj/demo/hg/export/operate/provider/DemoOperate.java)]

## 8. Useful Links

Demo: [demo-export-operate-provider](https://code.fanruan.com/hugh/demo-export-operate-provider)

Related interfaces:
- `com.fr.report.fun.ExportExtensionProcessor`
- `com.fr.stable.fun.ExcelExportCellValueProvider`
- `com.fr.form.stable.FormExportProcessor`
- `com.fr.report.fun.ExcelExportAppProvider`
- `com.fr.report.fun.CommentExcelProcessor`
- [com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)
- `com.fr.report.fun.FormatActionProvider`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

[demo-export-xml](https://code.fanruan.com/fanruan/demo-export-xml)

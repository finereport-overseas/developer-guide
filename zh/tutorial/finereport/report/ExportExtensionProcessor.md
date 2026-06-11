# ExportExtensionProcessor

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ExportExtensionProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ExportExtensionProcessor) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

对于单个类型的导出处理，通常我们都选择了ExportOperateProvider接口实现。但是如果我们需要对所有的报表导出都做某些特殊的操作，诸如对所有的导出信息推送、对导出的文件进行加密、备份等等操作时。我们需要一个更为统一的接口。那么在早期的帆软报表中就提供了ExportExtensionProcessor接口。该接口可以对所有的cpt类型的报表导出场景进行统一的拦截和处理。因为接口开得比较早，早期的决策报表是不支持导出的，所以这个接口也只能处理cpt报表的导出。

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.io.collection.ExportCollection;
import com.fr.stable.fun.mark.Immutable;
import com.fr.web.core.ReportSessionIDInfor;
import com.fr.web.core.TemplateSessionIDInfo;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义导出接口
 */
public interface ExportExtensionProcessor extends Immutable{

    String MARK_TAG = "ExportExtensionProcessor";

    int CURRENT_LEVEL = 1;


    /**
     * 导出后的文件名
     * @param req HTTP请求
     * @param sessionIDInfor 报表的会话信息
     * @return 文件名
     */
    String fileName(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor) throws Exception;

    /**
     * 导出所需要的集合
     * @param req HTTP请求
     * @param res HTTP响应
     * @param sessionIDInfor 报表的会话信息
     * @param format 导出格式
     * @param fileName 导出的文件名
     * @param isEmbed 是否是内置导出
     * @return 导出操作集合
     * @throws Exception 如果导出过程中出现异常则抛出此异常
     */
    ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res,
                                      ReportSessionIDInfor sessionIDInfor, String format,
                                      String fileName, boolean isEmbed) throws Exception;
}

```


```java
package com.fr.web.core.reserve;

import com.fr.data.NetworkHelper;
import com.fr.general.web.ParameterConstants;
import com.fr.io.collection.ExportCollection;
import com.fr.report.fun.impl.AbstractExportExtension;
import com.fr.web.Browser;
import com.fr.web.core.ReportSessionIDInfor;
import com.fr.web.core.TemplateSessionIDInfo;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Created by richie on 15/11/30.
 */
public class DefaultExportExtension extends AbstractExportExtension {

    public String fileName(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor) throws Exception {
        // carl:允许改名,没有就用报表名
        String fileName = NetworkHelper.getHTTPRequestFileNameParameter(req);

        //barry:op=fs程序报表用到
        if (fileName == null) {
            fileName = (String) sessionIDInfor.getParameterValue(ParameterConstants.__FILENAME__);
        }
        if (fileName == null) {
            //p:需要先获得导出报表的名字.
            fileName = sessionIDInfor.getWebTitle().replaceAll("\\s", "_");
            //shoc 导出文件名不能含有逗号 否则抛错
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
        <ExportExtensionProcessor class="your class name"/>
</extra-report>
```

## 六、原理说明

在沿用低版本的报表web服务体系的情况下，10.0中存在一个ExportService【op=export】的服务接口用于处理报表的导出操作。在dealWithExport方法中，会获取当前报表系统中生效的导出扩展申明（ExportExtension），从而进行对应的导出操作。

## 七、特殊限制说明

该接口是Immutable的，也就是是独占的，如果多个插件都有使用到，那么只会生效其中的一个。如果没有插件使用这个接口，则默认生效的是DefaultExportExtension 【注：也就是开发的时候可以参考这个类进行开发】

因为接口开在了整个导出的入口，所以会影响到所有其他的报表导出相关的接口，使用的时候需要非常谨慎。除特殊情况开发者尽量不要选择这个接口，容易跟其他插件产生冲突。【注：该接口仅用于一般定制，新商城插件严禁使用这个接口，避免造成大面积冲突】

该接口仅对cpt报表有效。

## 八、常用链接

demo地址：[demo-export-extension-processor](https://code.fanruan.com/hugh/demo-export-extension-processor)

com.fr.report.fun.ExportOperateProvider

com.fr.stable.fun.ExcelExportCellValueProvider

com.fr.form.stable.FormExportProcessor

com.fr.report.fun.ExcelExportAppProvider

com.fr.report.fun.CommentExcelProcessor

[com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

com.fr.report.fun.FormatActionProvider

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

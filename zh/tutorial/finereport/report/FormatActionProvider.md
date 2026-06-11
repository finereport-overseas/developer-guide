# FormatActionProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.FormatActionProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/FormatActionProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

帆软报表产品中的导出类接口比较多，其中存在FormatActionProvider这样的特殊的导出接口，该接口采用了类似filter的方式，直接把导出请求的处理进行了替换。从而实现对导出逻辑的干预。

场景方面该接口也只对不预览直接导出的场景生效（viewlet=xxx.cpt&format=xxx的方式）

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 针对报表一些特殊的format操作(相对于常见的导出文件)，比如填报直接提交，js打印pdf
 * Created by zack on 2016/3/3.
 */
public interface FormatActionProvider extends Mutable{

    String XML_TAG = "FormatActionProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 执行请求
     * @param req 请求
     * @param res 响应
     * @throws Exception 异常
     */
    void doAction(HttpServletRequest req, HttpServletResponse res) throws Exception;

    /**
     * 定义action对应的key,作用类似于cmd,默认取cmd
     * @return key
     */
    String getCMD();
}


```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 | 不支持仪表板 |
| BI | 4.0 | 支持 | 不支持仪表板 |
| BI | 5.1 | 支持 | 不支持仪表板 |
| BI | 5.1.2 | 支持 | 不支持仪表板 |
| BI | 5.1.3 | 支持 | 不支持仪表板 |

## 五、插件注册


```xml
<extra-report>
        <FormatActionProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

该接口的注册和生效都比较简单，通过下面的代码片段即可弄清楚。


```java
package com.fr.web.core.reserve;

import com.fr.log.FineLoggerFactory;
import com.fr.report.ExtraReportClassManager;
import com.fr.report.fun.FormatActionProvider;
import com.fr.web.core.action.PDFPrintPrintAction;

import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/**
 * Created by zack on 2016/3/3.
 */
public class FormatActionFactory {
    private static Map<String, Class<? extends FormatActionProvider>> detailMap = new HashMap<String, Class<? extends FormatActionProvider>>();

    static {
        detailMap.put("pt_print", PDFPrintPrintAction.class);//pdf打印
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

...

public class ReportletDealWith {
    ...
    /**
     * 处理模板
     *
     * @param req    http请求
     * @param res    http应答
     * @param weblet 模板
     * @throws Exception e
     */
    public static void dealWithReportlet(HttpServletRequest req, HttpServletResponse res, Weblet weblet) throws Exception {
        String sessionID = SessionPoolManager.generateSessionID(req, res, weblet);

        ...

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
     * 转向导出
     *
     * @param req       request
     * @param res       response
     * @param sessionID session id
     * @throws Exception e
     */
    public static void turnToExport(HttpServletRequest req, HttpServletResponse res, String sessionID) throws Exception {
        // 这里检查是不是输出成其他的形式,比如PDF, PDF_Activex, Excel等等..
        String format = WebUtils.getHTTPRequestParameter(req, "format");
        FormatActionProvider requestProcessor = FormatActionFactory.getReqProcessor(format);
        if (requestProcessor != null) {
            requestProcessor.doAction(req, res);
        } else {
            String embedParameter = WebUtils.getHTTPRequestParameter(req, ParameterConstants.EXPORT_PDF_EMBED);
            boolean embed = "true".equals(embedParameter); // 当且仅当参数值是true时,才嵌入
            ExportService.dealWithExport(req, res, sessionID, embed);
        }
    }

    ...
}

```

## 七、特殊限制说明

FormatActionProvider接口开得非常大，留给开发者放手施为的空间就多。但是同样的，因为接口开得太大，也容易不稳定或产生冲突。同时该接口实现后，如果开发者没有很好的设计，非常容易让其他所有的导出接口全部失效。需要使用该接口时需要非常谨慎，并且不推荐在商城插件中使用。

报表产品中，除了本身的导出，目前还有PDF打印（PDFPrintPrintAction format=pt_print）和自动提交（WriteAutoSubmitAction format=submit）两个功能使用了这个接口，也就是说，开发者可以借助这个接口对这两个Action进行替换做实现一些特定的需求。

从FormatActionFactory中也可以看出，FormatActionProvider接口的注册除了通过plugin.xml之外还可以调用FormatActionFactory.registerActionProvider("mycmd", MyAction.class);接口进行注册，开发者在实现时可以根据自己的需要灵活处理即可。

FormatActionProvider接口虽然是Mutable的，但是从生效原理中可以看出每次导出至多生效一个接口实例。对于重复的format后注册的会覆盖先注册的。

**⚠️ 注：在替换原有的Action逻辑时，最好先搞清楚原来的逻辑是怎么执行的，是否有其他接口被引用，如果有的话需要在实现自己的需求的时候，考虑好如何进行兼容或者跟用户协商好规避冲突和风险**

## 八、常用链接

demo地址：[demo-format-action-provider](https://code.fanruan.com/hugh/demo-format-action-provider)

com.fr.report.fun.ExportOperateProvider

com.fr.report.fun.ExportExtensionProcessor

com.fr.stable.fun.ExcelExportCellValueProvider

com.fr.form.stable.FormExportProcessor

com.fr.report.fun.ExcelExportAppProvider

com.fr.report.fun.CommentExcelProcessor

[com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

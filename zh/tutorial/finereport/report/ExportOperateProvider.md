# ExportOperateProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ExportOperateProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ExportOperateProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

帆软产品中一个非常方便的功能就是导出。使用频率也非常的高。对于常见的一些文件类型的导出，目前产品本身都已支持。但是在一些特殊的行业或者场景中，用户往往有自己的文件规范和导出规范，在帆软产品中专门开放了用于解决这类导出的接口。

可以用于导出的接口比较多，而ExportOperateProvider则主要用于新文件类型的导出使用。使用也跟一般的产品导出一致，通过URL中指定format参数调用。

该接口用于单一类型导出的处理场景，对于多种导出类型需要同时处理的可参见ExportExtensionProcessor接口

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.web.core.reserve.Operate;

/**
 * Created by richie on 16/1/19.
 * 自定义导出方式接口,推荐使用format=xxx的方式(xxx表示唯一的导出方式)
 */
public interface ExportOperateProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "ExportOperateProvider";

    /**
     * 导出准备操作
     *
     * @return 操作对象
     */
    Operate operate();

    /**
     * 导出类型
     *
     * @return 类型
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
 * 导出接口的抽象类
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
     * 导出的实体操作
     *
     * @param req             http请求
     * @param res             http响应
     * @param sessionProvider 模板访问会话信息
     * @param fileName        文件名
     * @return 导出实体操作对象
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

ExportCollection介绍

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |  |
| BI | 4.0 | 支持 |  |
| BI | 5.1 | 支持 |  |
| BI | 5.1.2 | 支持 |  |
| BI | 5.1.3 | 支持 |  |

## 五、插件注册


```xml
<extra-report>
        <ExportOperateProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

ExportFactory为所有导出类型的一个工厂类，通过format参数进行指定。工厂类加载时会注册所有插件中声明的新导出类型到OPERATE_MAP<String, Operate>对象中。实际导出服务被调用时，根据format参数，再创建具体的导出类型操作器进行导出。

## 七、特殊限制说明

OPERATE_MAP<String, Operate> 注册插件中的实例是在加载产品内置的导出类型之后的，而且是直接按format设置的，所以该接口在特殊情况下也可以用于替换原来的某些导出操作器的特性。只要markType接口返回值与需要替换的导出操作器的类型一致即可。

注：如果是作为替换产品固有的导出操作器使用时，该插件不得上架到商城插件！否则容易引发冲突！markType接口返回值就是前端format参数的值。

实际的开发过程中，为了具有较好的兼容性，开发者不要直接实现Operate接口，应继承BaseOperate类进行具体的接口实现。 

BaseOperate#setContent接口主要用于实现在响应头中声明文件类型、编码等信息。【[点击看例子](https://code.fanruan.com/hugh/demo-export-operate-provider/src/branch/10.0/src/main/java/com/tptj/demo/hg/export/operate/provider/DemoOperate.java)】

## 八、常用链接

demo地址：[demo-export-operate-provider](https://code.fanruan.com/hugh/demo-export-operate-provider)

com.fr.report.fun.ExportExtensionProcessor

com.fr.stable.fun.ExcelExportCellValueProvider

com.fr.form.stable.FormExportProcessor

com.fr.report.fun.ExcelExportAppProvider

com.fr.report.fun.CommentExcelProcessor

[com.fr.io.exporter.PDFExporterCreator](https://wiki.fanruan.com/display/PD/com.fr.io.exporter.PDFExporterCreator)

com.fr.report.fun.FormatActionProvider

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

[demo-export-xml](https://code.fanruan.com/fanruan/demo-export-xml)

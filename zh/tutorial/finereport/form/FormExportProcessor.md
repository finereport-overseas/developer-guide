# FormExportProcessor

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-form |
| 完整类名 | `com.fr.form.stable.FormExportProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/FormExportProcessor) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

随着决策报表在实际项目中的运用越来越多，但是决策报表一直都不支持导出。一部分用户对于导出需要把决策报表的内容重新用cpt报表制作一遍觉得太浪费时间了，对于决策报表支持导出的呼声也越来越多。标准的产品只支持决策报表的报表块内容导出。为了提供更丰富的扩展，官方开放了FormExportProcessor接口用于处理决策报表的导出需要。不过目前这个接口尚不完全成熟，存在的限制颇多，也不够稳定，现阶段更多的是用于简单决策报表的导出处理场景。

## 三、接口介绍

```java
package com.fr.form.stable;

import com.fr.stable.fun.mark.Immutable;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 表单导出接口, 通过传不同的format来导出Excel, pdf等等
 *
 * Created by Administrator on 2015/12/18 0018.
 */
public interface FormExportProcessor extends Immutable {

    String MARK_STRING = "FormExportProcessor";

    int CURRENT_LEVEL = 3;

    /**
     * 进行导出
     *
     * @param req       http请求
     * @param res       http应答
     * @param sessionID 会话ID
     * @throws Exception
     */
    void dealWithExport(HttpServletRequest req, HttpServletResponse res, String sessionID);

}
```

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
<extra-form>
        <FormExportProcessor class="your class name"/>
</extra-form>
```

## 六、原理说明

决策报表预览时，后台通过Formlet#dealWeblet调用了FormletDealWith#dealWithFormlet方法进行计算响应。在这个dealWithFormlet中，如果请求中包含了format参数，则会单独走导出处理，获取到插件中定义的决策报表导出类申明并执行响应。

## 七、特殊限制说明

该接口是Immutable的，也就是是独占的，如果多个插件都有使用到，那么只会生效其中的一个。如果没有插件使用这个接口，标准产品的决策报表默认是不支持任何导出的。

在实现导出功能时，开发者可以通过sessionID这个入参获取到决策报表的结果对象，并读取布局和组件信息生成自己想要的导出效果。【[点击看例子](https://code.fanruan.com/hugh/demo-form-export-processor/src/branch/10.0/src/main/java/com/tptj/demo/hg/form/export/processor/Demo.java)】

接口仅对决策报表有效。

> ⚠️ 注：该接口适用面小，开发成熟导出的功能难度较大，产品耦合高，稳定性一般。一般不建议开发者使用！导出场景尽可能都在cpt报表上去实现。否则随着开发的细节深入，开发者会发现很多很难处理的点（适应，兼容，布局，窗口切割等等的）。

## 八、常用链接

demo地址：[demo-form-export-processor](https://code.fanruan.com/hugh/demo-form-export-processor)

相关接口：ExportOperateProvider、ExportExtensionProcessor、ExcelExportAppProvider、ExcelExportCellValueProvider、CommentExcelProcessor

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

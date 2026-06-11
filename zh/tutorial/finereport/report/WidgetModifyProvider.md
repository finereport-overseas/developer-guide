# WidgetModifyProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report (stable) |
| 完整类名 | `com.fr.stable.fun.WidgetModifyProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/WidgetModifyProvider) |

---

## 一、特殊名词介绍

## 二、背景、场景介绍

帆软报表在导出时标准产品中对部分控件导出PDF进行了图片转换导出（导出控件）。WidgetModifyProvider主要用于当前产品不支持导出的控件，或导出的控件样式不满足需要的导出和打印场景中。

开发者可以将控件转换成业务需要的样式的图片进行打印或导出。

## 三、接口介绍


```java
package com.fr.stable.fun;

import com.fr.form.ui.Widget;
import com.fr.report.cell.CellElement;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

import java.awt.image.BufferedImage;

/**
 * 该接口用于更改控件的各种属性
 *
 */
public interface WidgetModifyProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "WidgetModifyProvider";

    /**
     * 判断接受类型
     *
     * @param widget 控件
     * @return 是否胜任处理
     */
    boolean accept(Widget widget);

    /**
     * 控件转成图片，用于导出打印
     *
     * @param ce     当前格子
     * @param pxWidth   宽度
     * @param pxHeight  高度
     * @param calculator 算子
     * @return 控件转化的图片
     */
    BufferedImage toImage(CellElement ce, int pxWidth, int pxHeight, CalculatorProvider calculator);
}


```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
        <WidgetModifyProvider class="your class name"/>
</extra-report>
```

## 六、原理说明


```java
package com.fr.write.cal;
...
final public class WB extends CalculatableResWorkSheet implements WriteECReport, ShrinkFitable, WBProvider {
	...
	/**
     * 将结果报表中的控件处理成图片, 用于导出打印
     *
     * @param resultbook 结果报表
     * @return 处理后的结果报表
     * @date 2014-9-24-上午8:51:41
     */
    public ResultECReport processWidgetAsImage(ResultECReport resultbook) {
        if (!CustomConfigManager.getInstance().isPrintWidget()) {
            return resultbook;
        }

        WB cloneWB;
        try {
            cloneWB = (WB) resultbook.clone();
        } catch (CloneNotSupportedException e) {
            cloneWB = (WB) resultbook;
        }

        Iterator it = cloneWB.cellIterator();
        DynamicUnitList rowHeightList = this.getRowHeightList_DEC();
        DynamicUnitList columnWidthList = this.getColumnWidthList_DEC();

        while (it.hasNext()) {
            BCE_WRITE ce = (BCE_WRITE) it.next();
            Widget widget = (Widget) ce.getOptionalAttributes().get(WriteCellElementAttribute.WIDGET);
            processWidgetAsImage(widget, rowHeightList, columnWidthList, ce);
        }

        return cloneWB;
    }

    /**
     * 处理单个控件, 并将其转化为图片
     *
     * @param widget          当前控件
     * @param rowHeightList   行高list
     * @param columnWidthList 列宽list
     * @param ce              当前格子
     * @date 2014-9-24-上午8:53:24
     */
    private void processWidgetAsImage(Widget widget, DynamicUnitList rowHeightList,
                                      DynamicUnitList columnWidthList, BCE_WRITE ce) {
        if (widget == null || !widget.isVisible()) {
            return;
        }

        int resolution = Constants.DEFAULT_WEBWRITE_AND_SCREEN_RESOLUTION;
        //获取格子的宽高
        int pxHeight = rowHeightList.getRangeValue(ce.getRow(), ce.getRow() + ce.getRowSpan()).toPixI(resolution);
        int pxWidth = columnWidthList.getRangeValue(ce.getColumn(), ce.getColumn() + ce.getColumnSpan()).toPixI(resolution);
        // 转换图片
        BufferedImage image = drawWidget(widget, ce, pxHeight, pxWidth);

        if (image != null) {
            // 处理完, 控件已经不需要了, 省的其他的打印还要再走一遍
            ce.setValue(image);
            //单元格本身还定义了形态, 那么就要把presentvalue也弄掉
            ce.setPresentValue(image);
        }
        ce.getOptionalAttributes().remove(WriteCellElementAttribute.WIDGET);
    }

    private BufferedImage drawWidget(Widget widget, CellElement ce, int pxHeight, int pxWidth) {
        ExtraReportClassManager extraReportClassManager = PluginModule.getAgent(PluginModule.ExtraReport);
        if (extraReportClassManager != null) {
            Set<WidgetModifyProvider> providers = extraReportClassManager.getArray(WidgetModifyProvider.MARK_STRING);
            for (WidgetModifyProvider provider : providers) {
                if (provider.accept(widget)) {
                    return provider.toImage(ce, pxWidth, pxHeight, this.getCalculator());
                }
            }
        }
        return widget.toImage(ce.getValue(), pxWidth, pxHeight, ce.getStyle(), this.getCalculator());
    }
	...
}
```

## 七、特殊限制说明

从调用原理中可以知道，导出控件要生效必须先保障CustomConfigManager.getInstance().isPrintWidget() 是开启的，为了方便修改配置，可以安装[可视化配置插件](https://market.fanruan.com/plugin/1052a471-0239-4cd8-b832-045d53182c5d)  。并将CustomConfig.printWidget设置为true。

本身Widget对象是有一个toImage函数的，故如果本身已经是自己开发的控件，要打印导出，则不需要再使用WidgetModifyProvider接口来处理了！

## 八、常用链接

demo地址：[demo-widget-modify-provider](https://code.fanruan.com/hugh/demo-widget-modify-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。禁止用于任何商业用途，若作为商用一切后果责任由使用者自行承担。

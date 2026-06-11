# CellValueProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.CellValueProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/CellValueProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

CellValueProvider接口用于模板计算时修改单元格的真实值、计算时调整单元格的实际大小、输出html页面内容时调整页面的显示值（非单元格的显示值）。

主要用于以下几种场景：

1、纯展示报表的词条转义、国际化等大量的单元格值得转换。

2、单元格值得格式化处理（主要在早期没有富文本时，处理文档型报表使用）

3、对特殊类型值得单元格大小调整

4、大量得展示与导出内容的差异化处理（展示于导出内容需要有差异的场景）

## 三、接口介绍


```java
package com.fr.report.fun;


import com.fr.base.Style;
import com.fr.report.cell.CellElement;
import com.fr.report.cell.cellattr.CellGUIAttr;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.CalculatorProvider;

/**
 * 单元格中间处理器
 */
public interface CellValueProvider extends Mutable{
    String MARK_STRING = "CellValueProvider";
	int CURRENT_LEVEL = 1;

	/**
	 * 获取单元格的值
	 * 
	 * @param oriValue 单元格未计算的值
	 * @param ca 算子
	 * @return 返回计算后的单元格值
	 */
    Object process(Object oriValue, CalculatorProvider ca);

    /**
     * 转换为html之前的处理单元格值接口
     * 例如: 单元格输出到web端不想用默认的显示方式, 想将其转为图片, 转为富文本等等.
     *
     * @param guiAttr 显示属性
     * @param height 单元格高度
     * @param oriValue 原值
     * @param style 样式
     * @param width 单元格宽度
     * @return 处理后的值
     */
    Object processBeforeToTag(Object oriValue, CellGUIAttr guiAttr, Style style, int width, int height);
    
    /**
     * 计算该单元格的真实高度和宽度
     * @param cellElement 单元格
     * @param height	单元格的默认高度
     * @param width		单元格的默认宽度
     * @param mode		单元格的行高列宽调整模式
     * @return 真实的行高或者列宽，根据模式决定
     */
    int prepare2ShrinkRealWidthOrHeight4Cell(CellElement cellElement,int height,int width ,int mode);
}
```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
	<CellValueProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

该接口在需要调用的地方通过：Set<CellValueProvider> providers = ExtraReportClassManager.getInstance().getArray(CellValueProvider.MARK_STRING); 获取到插件中所有申明单元格中间处理器接口实例。

产品中主要在SE、CellHtmlWriter、ECReport中被获取生效（因这部分逻辑涉及敏感信息，无法单独透明，开发者记住相关场景和接口的用法即可）

## 七、特殊限制说明

Object process(Object oriValue, CalculatorProvider ca); 中oriValue是模板中的原始单元格值。

Object processBeforeToTag(Object oriValue, CellGUIAttr guiAttr, Style style, int width, int height);中oriValue是经过process转换后的值。processBeforeToTag仅对一般web(非H5)展示的报表内容有效。打印导出均不受该接口方法影响，该方法仅改变了展示的内容，不改变实际单元格的真实值或者显示值

int prepare2ShrinkRealWidthOrHeight4Cell(CellElement cellElement,int height,int width ,int mode);中height和width表示当前的单元格的像素宽度，mode表示单元格设置的是自动调整行高（1）还是列宽（2），对应的也只能修改行高或者列宽（即不能对同一个单元格即调整行高又调整列宽）。此时单元格的值也是经过process转换后的值。

## 八、常用链接

demo地址：[demo-cell-value-provider](https://code.fanruan.com/hugh/demo-cell-value-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

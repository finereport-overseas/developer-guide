# CellPropertyProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.CellPropertyProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/CellPropertyProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

为单元格添加额外属性的接口，目前主要作用就是给移动端输出JSON配置用。也可以用于保存一些单元格的配置，来配合HtmlWriter或者Actor接口在计算中引用配置。

注：虽然可以用于配合HtmlWriter或者Actor接口在计算中引用配置使用，但是由于目前该接口设计上并不够人性化，使用复杂度相对于增加一个配置来说，开发难度稍微显得有点大，如果不是实在没有其他方案的情况下，开发者尽量先避开使用该接口，后续官方会重新开放出相同功能但使用更简单的接口。

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.json.JSONException;
import com.fr.json.JSONObject;
import com.fr.report.cell.CellElement;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.Repository;
import com.fr.stable.xml.XMLable;

/**
 * 单元格属性接口
 * @author zack
 * @version 10.0
 * Created by zack on 2020/7/14
 */
public interface CellPropertyProvider extends Mutable, XMLable {
    String MARK_STRING = "CellPropertyProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 属性唯一标记(xml节点tagname)
     * @return
     */
    String xmlTag();

    /**
     * 将属性输出json
     *
     * @param cellJson    单元格json
     * @param cellElement 当前单元格元素
     * @throws JSONException
     * @repository repository 上下文
     */
    void mixinCellJson(JSONObject cellJson, Repository repository, CellElement cellElement) throws JSONException;

    /**
     * clone
     * @return
     */
    CellPropertyProvider clone();
}
```

该接口通常会额外配合CellPropertyPaneProvider接口使用


```java
package com.fr.design.fun;

import com.fr.design.cell.CellElementPropertyComponent;

/**
 * 单元格设置(属性)扩展接口
 * @author zack
 * @version 10.0
 * Created by zack on 2020/7/14
 */
public interface CellPropertyPaneProvider extends PropertyItemPaneProvider {

    /**
     * 构造单元格属性面板,面板实现需要使用单例模式实现
     * @return 面板类
     */
    CellElementPropertyComponent getSingletonCelPropertyPane();
}

```


```java
package com.fr.design.fun;

import com.fr.design.mainframe.PaneHolder;
import com.fr.design.mainframe.PropertyItemBean;
import com.fr.stable.fun.mark.Mutable;
import org.jetbrains.annotations.Nullable;

/**
 * created by Harrison on 2020/03/23
 **/
public interface PropertyItemPaneProvider<T> extends Mutable {
    
    int CURRENT_LEVEL = 1;
    
    String XML_TAG = "PropertyItemPaneProvider";
    
    int FIRST = 100;
    
    int LAST = -100;
    
    /**
     * 独一无二的标志
     *
     * @return 标志
     */
    String key();
    
    /**
     * 配置属性
     *
     * @return 熟悉
     */
    PropertyItemBean getItem();
    
    /**
     * 面板持有者
     *
     * @param clazz 类型
     * @return 持有者
     */
    @Nullable
    PaneHolder<T> getPaneHolder(Class<?> clazz);
    
    /**
     * 想要替代的类型
     *
     * @return 替代类型
     */
    String replaceKey();
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
	<CellPropertyProvider class="your class name"/>
</extra-report>
<extra-designer>
	<PropertyItemPaneProvider class="your class name"/>
</extra-designer>
```

## 六、原理说明

该接口在需要调用的地方通过：Set<CellPropertyProvider> extraProperties = PluginModule.getAgent(PluginModule.ExtraReport).getArray(CellPropertyProvider.MARK_STRING); 获取到插件中所有申明单元格扩展属性。

在产品中主要通过以下方式生效：插件单元格属性类型统一通过CellPropertyManager进行管理，报表计算逻辑中通过CellPropertyManager.getAllCellPropertiesAsMap()读取到所有插件中的扩展单元格属性，并通过key匹配对应的面板展现。配置后通过插件内部自身的事件处理进行保存（这也是这个接口使用比较复杂的地方）。

## 七、特殊限制说明

插件实现的属性类不能包含不可序列化的属性对象,比如BufferImage对象.如果带了,单元格的复制粘贴会报序列化错误，需要自己想办法通过其他方式实现业务,比如存图片名字,或者base64字符串。

## 八、常用链接

demo地址：[demo-cell-property-provider](https://code.fanruan.com/hugh/demo-cell-property-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

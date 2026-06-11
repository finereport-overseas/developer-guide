# PreviewProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.PreviewProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/PreviewProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

PreviewProvider是用于与ActorProvider配套的，设计器预览模式的接口。一般说来如果插件中提供了一种新的预览方式，对应的设计器上就要提供一个新的预览方式的选项。PreviewProvider接口就是用于提供设计器上的新预览方式选项的。

## 三、接口介绍


```java
package com.fr.design.fun;

import com.fr.design.mainframe.JTemplate;
import com.fr.stable.Filter;
import com.fr.stable.fun.mark.Mutable;

import java.util.Map;

/**
 * @author richie
 * @date 2015-03-19
 * @since 8.0
 * 自定义预览方式接口
 */
public interface PreviewProvider extends Mutable, Filter<JTemplate> {

    String MARK_STRING = "PreviewProvider";

    int CURRENT_LEVEL = 1;


    /**
     * 下拉弹出菜单的名字
     * @return 弹出菜单名字
     */
    String nameForPopupItem();

    /**
     * 下拉弹出菜单的图标路径
     * @return 图标路径
     */
    String iconPathForPopupItem();

    /**
     * 大图标路径
     * @return 大图标路径
     */
    String iconPathForLarge();

    /**
     * 点击下拉菜单时触发的事件
     * @param jt 当前的模板对象
     */
    void onClick(JTemplate<?, ?> jt);

    /**
     * 用于标记预览类型的整数
     * @return 预览类型
     */
    int previewTypeCode();

    /**
     * 该种预览方式所携带的默认参数集合
     * @return 参数集合
     */
    Map<String, Object> parametersForPreview();

    /**
     * 该种预览方式下对模板的处理方式
     * 分页填报等需要计算输出模板，移动端预览直接输出url
     * @return 处理方式
     */
    String getActionType();


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
<extra-designer>
        <PreviewProvider class="your class name"/>
</extra-designer>
```

## 六、原理说明


```java
package com.fr.design.mainframe;

...

public class JWorkBook extends JTemplate<WorkBook, WorkBookUndoState> {

   ...

    /**
     * 是否支持预览
     *
     * @return 预览接口
     */
    @Override
    public PreviewProvider[] supportPreview() {
        PreviewProvider[] templatePreviews = super.supportPreview();
        return ArrayUtils.addAll(new PreviewProvider[]{
                new PagePreview(), new WritePreview(), new ViewPreview(), new WriteEnhancePreview(), new MobilePreview()
        }, templatePreviews);
    }

    /**
     * 预览菜单项
     *
     * @return 预览菜单项
     */
    @Override
    public UIMenuItem[] createMenuItem4Preview() {
        List<UIMenuItem> menuItems = new ArrayList<UIMenuItem>();
        PreviewProvider[] previewProviders = supportPreview();
        for (final PreviewProvider provider : previewProviders) {
            UIMenuItem item = new UIMenuItem(provider.nameForPopupItem(), BaseUtils.readIcon(provider.iconPathForPopupItem()));
            item.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    provider.onClick(JWorkBook.this);
                }
            });
            menuItems.add(item);
        }
        return menuItems.toArray(new UIMenuItem[menuItems.size()]);
    }

    

    @Override
    /**
     *
     */
    public Icon getPreviewLargeIcon() {
        PreviewProvider provider = getPreviewType();
        String iconPath = provider.iconPathForLarge();
        return BaseUtils.readIcon(iconPath);
    }

    ...
}


```

## 七、特殊限制说明

previewTypeCode接口方法目前从设计上来说存在一定的缺陷。没有考虑到实际扩展后的序号可能多个插件之间容易冲突。相对于数字，文本语义更明确，冲突的概率就小得多了。建议开发者统一返回0就可以了，然后通过parametersForPreview接口方法指定op参数来指定具体的ActorProvider进行关联

getActionType接口方法，主要用于指定URL中承接报表路径的参数是什么，默认是viewlet，如果对应的ActorProvider有特殊要求需要改变这个参数名的可以进行指定

accept接口是当设计器预览点击时，进行的是否适用于该接口的入口函数，默认不需要实现

## 八、常用链接

demo地址：[demo-preview-provider](https://code.fanruan.com/hugh/demo-preview-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

# WaterMarkProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report-core |
| 完整类名 | `com.fr.report.core.watermark.WaterMarkProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/WaterMarkProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

在8.0和9.0版本中，为了实现水印效果。使用了背景接口来处理。但该接口作用的地方比较多，使用起来比较混乱。加上各开发者都在使用背景接口导致最终冲突较多。可用性不足。因此10.0专门争对水印场景实现了一个单独的水印功能，并包含了全局和单个模板的不同水印配置。但该功能只能预先配置好水印的有无和生效规则，对于一些特殊的根据某些特殊接口或条件控制的水印展示难以满足需要。故在22年2月的10和11版本中正式发布了插件的方式来引入自定义的水印规范。

## 三、接口介绍


```java
package com.fr.report.core.watermark;

import com.fr.base.io.AttrMark;
import com.fr.base.iofile.attr.WatermarkAttr;
import com.fr.stable.fun.mark.Mutable;

/**
 * 一个水印的加载器
 *
 * @author Hoky
 * @date 2021/12/20
 */

public interface WaterMarkProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "WaterMarkProvider";

    /**
     * 用于判断当前加载的水印有效性
     *
     * @return 当前加载器的水印加载是否有效
     */
    boolean isWaterMarkValid(AttrMark template);

    WatermarkAttr getWaterMark(AttrMark template);

    /**
     * 水印加载的等级
     *
     * @return 加载的水印的加载顺序
     */
    WaterMarkProvideLevel getWaterMarkProvideLevel();
}
```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| FR | 11.0 | 支持 |  |

## 五、插件注册


```xml
<extra-report>
	<WaterMarkProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

注：该接口通常不作为独立接口使用。一般会配合其他配置或者服务接口联合生效。

其中首先要注意的方法是getWaterMarkProvideLevel方法，该方法返回了接口的生效优先级，值为枚举类型


```java
package com.fr.report.core.watermark;

/**
 * 水印加载的优先级
 *
 * @author Hoky
 * @date 2021/12/20
 */
public enum WaterMarkProvideLevel {
    PLUGIN(1),
    TEMPLATE(2),
    SERVER(3),
    OTHER(4);

    private Integer level;

    WaterMarkProvideLevel(int level) {
        this.level = level;
    }

    public Integer getLevel() {
        return level;
    }
}
```

优先级从高到底是 插件水印 > 模板水印 > 服务器全局水印 > 其他水印 。 其中其他水印是保留级别，一般无实意。

优先级相同的则按从前往后的顺寻进行判单生效。所有的水印适配器最终只会生效一个。具体判断是否生效的方法为接口：isWaterMarkValid，生效机制如下


```java
package com.fr.report.core.watermark;

import com.fr.base.io.AttrMark;
import com.fr.base.iofile.attr.WatermarkAttr;
import com.fr.event.Event;
import com.fr.event.EventDispatcher;
import com.fr.event.Listener;
import com.fr.log.FineLoggerFactory;
import com.fr.plugin.context.PluginContext;
import com.fr.plugin.injectable.PluginModule;
import com.fr.plugin.observer.PluginEventType;
import com.fr.report.ExtraReportClassManager;
import com.fr.stable.Filter;
import com.fr.workspace.WorkContext;

import java.util.Comparator;
import java.util.Set;
import java.util.TreeSet;
import java.util.stream.Collectors;

/**
 * 水印制造器
 *
 * @author Hoky
 * @date 2021/12/20
 */
public class WaterMarkFactory {
    private static final Set<WaterMarkProvider> SORTED_LOADING_SET = new TreeSet<>(
            Comparator.comparing(o -> o.getWaterMarkProvideLevel().getLevel())
    );

    public static final WaterMarkProvider DEFAULT_MAKER = new DefaultWaterMarkProvider();

    private WaterMarkFactory() {
        SORTED_LOADING_SET.add(DEFAULT_MAKER);
        SORTED_LOADING_SET.add(new ServerWaterMarkProvider());
        SORTED_LOADING_SET.add(new TemplateWaterMarkProvider());
    }

    /**
     * 默认的加载水印，根据顺序来加载
     *
     * @param template
     * @return
     */
    public WatermarkAttr getWaterMark(AttrMark template) {
        for (WaterMarkProvider waterMarkProvider : SORTED_LOADING_SET) {
            synchronized (this) {
                if (waterMarkProvider.isWaterMarkValid(template)) {
                    return waterMarkProvider.getWaterMark(template);
                }
            }
        }
        return new WatermarkAttr();
    }

    /**
     * 根据加载等级来加载水印
     *
     * @param template  模板
     * @param loadLevel 加载等级
     * @return
     */
    public WatermarkAttr getWaterMark(AttrMark template, WaterMarkProvideLevel loadLevel) {
        WaterMarkProvider waterMarkProvider = SORTED_LOADING_SET.stream()
                .filter(loader -> loader.getWaterMarkProvideLevel().equals(loadLevel))
                .findFirst().orElse(DEFAULT_MAKER);
        return waterMarkProvider.getWaterMark(template);
    }

    /**
     * 注册一个水印加载器
     *
     * @param loader
     */
    public void addLoader(WaterMarkProvider loader) {
        SORTED_LOADING_SET.add(loader);
    }

    public synchronized void installPluginLoader() {
        ExtraReportClassManager provider = PluginModule.getAgent(PluginModule.ExtraReport);
        if (provider != null) {
            Set<WaterMarkProvider> waterMarkProviders = provider.getArray(WaterMarkProvider.XML_TAG);
            if (!waterMarkProviders.isEmpty()) {
                waterMarkProviders.forEach(this::addLoader);
            }
        }
    }

    public synchronized void uninstallPluginLoader() {
        Set<WaterMarkProvider> pluginWaterMarkProviders = SORTED_LOADING_SET.stream()
                .filter(loader -> loader.getWaterMarkProvideLevel().equals(WaterMarkProvideLevel.PLUGIN))
                .collect(Collectors.toSet());
        SORTED_LOADING_SET.removeAll(pluginWaterMarkProviders);
    }

    private final static WaterMarkFactory FACTORY = new WaterMarkFactory();

    public static WaterMarkFactory getFactory() {
        return FACTORY;
    }

    public void initPluginListener() {
        //注入插件实现水印加载器
        try {
            installPluginLoader();
            Filter<PluginContext> filter = context ->
                    context.getRuntime().contain(WaterMarkProvider.XML_TAG) && WorkContext.getCurrent().isLocal();

            FineLoggerFactory.getLogger().info("init PluginListener");
            EventDispatcher.listen(PluginEventType.AfterRun, new Listener<PluginContext>() {
                @Override
                public void on(Event event, PluginContext pluginContext) {
                    // 资源仓库插件运行时加载一次加载器
                    installPluginLoader();
                }
            }, filter);

            EventDispatcher.listen(PluginEventType.BeforeStop, new Listener<PluginContext>() {
                @Override
                public void on(Event event, PluginContext pluginContext) {
                    // 资源仓库插件关闭时卸载插件加载器
                    uninstallPluginLoader();
                }
            }, filter);
        } catch (Throwable e) {
            FineLoggerFactory.getLogger().error(e.getMessage(), e);
        }

    }

}


```

其中public WatermarkAttr getWaterMark(AttrMark template) 方法所示过程说明了水印实际生效的过程。

注：public WatermarkAttr getWaterMark(AttrMark template, WaterMarkProvideLevel loadLevel)方法目前为工具和功能支撑提供，一般不用。

而内置的模板水印和全局水印判断生效的方法如下


```java
package com.fr.report.core.watermark;

import com.fr.base.io.AttrMark;
import com.fr.base.iofile.attr.WaterMarkProvideConstant;
import com.fr.base.iofile.attr.WatermarkAttr;

/**
 * 模板水印加载器
 *
 * @author Hoky
 * @date 2021/12/20
 */
public class TemplateWaterMarkProvider extends AbstractWaterMarkProvider {

    @Override
    public boolean isWaterMarkValid(AttrMark template) {
        return template != null && (template.getAttrMark(WatermarkAttr.XML_TAG) != null
                && ((WatermarkAttr) template.getAttrMark(WatermarkAttr.XML_TAG)).isValid());
    }

    @Override
    public WatermarkAttr getWaterMark(AttrMark template) {
        WatermarkAttr attrMark = (WatermarkAttr) template.getAttrMark(WatermarkAttr.XML_TAG).clone();
        attrMark.setWaterMarkProvider(WaterMarkProvideConstant.TEMPLATE);
        return attrMark;
    }

    @Override
    public WaterMarkProvideLevel getWaterMarkProvideLevel() {
        return WaterMarkProvideLevel.TEMPLATE;
    }

}
```


```java
package com.fr.report.core.watermark;

import com.fr.base.io.AttrMark;
import com.fr.base.iofile.attr.WaterMarkProvideConstant;
import com.fr.base.iofile.attr.WatermarkAttr;
import com.fr.decision.config.WatermarkConfig;
import com.fr.decision.security.WatermarkData;

/**
 * 服务器水印加载器
 *
 * @author Hoky
 * @date 2021/12/20
 */
public class ServerWaterMarkProvider extends AbstractWaterMarkProvider {
    @Override
    public boolean isWaterMarkValid(AttrMark template) {
        return WatermarkConfig.getInstance().isValid();
    }

    @Override
    public WatermarkAttr getWaterMark(AttrMark template) {
        WatermarkData watermarkData = WatermarkConfig.getInstance().getWatermarkData();
        WatermarkAttr watermarkAttr = new WatermarkAttr(watermarkData);
        //默认从模板水印中读出来的valid为false，因为valid在设计之初只对模板水印有效
//        watermarkAttr.setValid(true);
        watermarkAttr.setWaterMarkProvider(WaterMarkProvideConstant.SERVER);
        return watermarkAttr;
    }

    @Override
    public WaterMarkProvideLevel getWaterMarkProvideLevel() {
        return WaterMarkProvideLevel.SERVER;
    }

}
```

## 七、特殊限制说明

通常建议开发者根据需要从这两个适配器进行集成再单独改造自己想要的功能。合理的使用水印的优先级会更容易实现自己的需求（并不是一定要把优先级设置成PLUGIN级别）

## 八、常用链接

demo地址：[demo-water-mark-provider](https://code.fanruan.com/hugh/demo-water-mark-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

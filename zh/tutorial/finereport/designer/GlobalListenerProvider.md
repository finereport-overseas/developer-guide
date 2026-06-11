# GlobalListenerProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.GlobalListenerProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/GlobalListenerProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

FineReport设计器中本身提供了一组快捷键操作，方便习惯纯键盘操作的用户获得更好的体验。但不同的用户需要的快捷键操作不尽相同，很多设计器的功能本身又是在插件中扩展出来的。为了更灵活的让用户使用快捷键。设计器中开放了GlobalListenerProvider接口。允许开发者在自己的插件中扩展相应的快捷键功能。用于捕获设计器全局的键盘输入事件。该接口一般不独立使用，更多用于配合设计器的其他功能实现一同生效。

## 三、接口介绍


```java
package com.fr.design.fun;
 
 
import com.fr.stable.fun.mark.Mutable;
 
import java.awt.event.AWTEventListener;
 
/**
 * Created by zack on 2015/8/17.
 * 全局事件监听
 */
public interface GlobalListenerProvider extends Mutable{
 
    String XML_TAG = "GlobalListenerProvider";
 
    int CURRENT_LEVEL = 1;
 
    /**
     * 返回一个AWT监听事件给系统注册
     * @return AWT监听事件
     */
    AWTEventListener listener();
}

```


```java
package com.fr.design.fun.impl;

import com.fr.design.ExtraDesignClassManager;
import com.fr.design.fun.GlobalListenerProvider;
import com.fr.general.GeneralContext;
import com.fr.plugin.context.PluginContext;
import com.fr.plugin.context.PluginRuntime;
import com.fr.plugin.injectable.PluginModule;
import com.fr.plugin.manage.PluginFilter;
import com.fr.plugin.observer.PluginEvent;
import com.fr.plugin.observer.PluginEventListener;
import com.fr.plugin.observer.PluginEventType;

import java.awt.*;
import java.awt.event.AWTEventListener;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/**
 * Created by juhaoyu on 2017/6/15.
 * 管理正在运行中插件的GlobalListenerProvider接口
 */
public class GlobalListenerProviderManager {
    
    private static final GlobalListenerProviderManager INSTANCE = new GlobalListenerProviderManager();
    
    private Map<GlobalListenerProvider, AWTEventListener> map = new HashMap<>();
    
    private GlobalListenerProviderManager() {}
    
    public static GlobalListenerProviderManager getInstance() {
        
        return INSTANCE;
    }
    
    public void init() {
        
        Set<GlobalListenerProvider> providers = ExtraDesignClassManager.getInstance().getArray(GlobalListenerProvider.XML_TAG);
        addAWTEventListeners(providers);
        listenPlugin();
    }
    
    private void listenPlugin() {
        
        PluginFilter filter = new PluginFilter() {
            
            @Override
            public boolean accept(PluginContext context) {
                
                return context.contain(PluginModule.ExtraDesign, GlobalListenerProvider.XML_TAG);
            }
        };
        PluginEventListener onRun = new PluginEventListener() {
            
            @Override
            public void on(PluginEvent event) {
                
                PluginRuntime runtime = event.getContext().getRuntime();
                Set<GlobalListenerProvider> providers = runtime.get(PluginModule.ExtraDesign, GlobalListenerProvider.XML_TAG);
                addAWTEventListeners(providers);
            }
        };
        PluginEventListener onStop = new PluginEventListener() {
            
            @Override
            public void on(PluginEvent event) {
                
                PluginRuntime runtime = event.getContext().getRuntime();
                Set<GlobalListenerProvider> providers = runtime.get(PluginModule.ExtraDesign, GlobalListenerProvider.XML_TAG);
                removeAWTEventListeners(providers);
            }
        };
        GeneralContext.listenPlugin(PluginEventType.AfterRun, onRun, filter);
        GeneralContext.listenPlugin(PluginEventType.BeforeStop, onStop, filter);
        
        
    }
    
    private void addAWTEventListeners(Set<GlobalListenerProvider> providers) {
        
        AWTEventListener listener;
        for (GlobalListenerProvider provider : providers) {
            listener = provider.listener();
            Toolkit.getDefaultToolkit().addAWTEventListener(listener, AWTEvent.KEY_EVENT_MASK);
            add(provider, listener);
        }
    }
    
    private void removeAWTEventListeners(Set<GlobalListenerProvider> providers) {
        
        AWTEventListener listener;
        for (GlobalListenerProvider provider : providers) {
            listener = pop(provider);
            Toolkit.getDefaultToolkit().removeAWTEventListener(listener);
        }
    }
    
    private synchronized void add(GlobalListenerProvider provider, AWTEventListener listener) {
        
        map.put(provider, listener);
    }
    
    private synchronized AWTEventListener pop(GlobalListenerProvider provider) {
        
        return map.remove(provider);
    }
 
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
        <GlobalListenerProvider class="your class name"/>
</extra-designer>
```

## 六、原理说明

接口只能在设计器中被调用，在需要调用的地方通过Set<GlobalListenerProvider> providers = ExtraDesignClassManager.getInstance().getArray(GlobalListenerProvider.XML_TAG);获取到插件中所有申明的全局事件扩展。

默认的产品中主要在：上面的GlobalListenerProviderManager中进行监听生效。

## 七、特殊限制说明

暂无

## 八、常用链接

[demo-global-listener-provider](https://code.fanruan.com/hugh/demo-global-listener-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

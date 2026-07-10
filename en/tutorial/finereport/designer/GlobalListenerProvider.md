# GlobalListenerProvider

| Property | Value |
| --- | --- |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.GlobalListenerProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/GlobalListenerProvider) |

---

## 1. Special Terms

None

## 2. Background and Use Cases

FineReport Designer provides a set of built-in keyboard shortcuts to improve the experience for users who prefer keyboard-based operations. However, different users need different shortcuts, and many designer features are themselves extended through plugins. To provide more flexibility, the designer exposes the `GlobalListenerProvider` interface, allowing developers to extend keyboard shortcuts in their own plugins. This interface captures global keyboard input events in the designer and is generally not used in isolation — it is more commonly combined with other designer feature implementations.

## 3. Interface Introduction

```java
package com.fr.design.fun;


import com.fr.stable.fun.mark.Mutable;

import java.awt.event.AWTEventListener;

/**
 * Created by zack on 2015/8/17.
 * Global event listener.
 */
public interface GlobalListenerProvider extends Mutable{

    String XML_TAG = "GlobalListenerProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Returns an AWT event listener to be registered with the system.
     * @return AWT event listener
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
 * Manages GlobalListenerProvider instances for currently running plugins.
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

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration

```xml
<extra-designer>
        <GlobalListenerProvider class="your class name"/>
</extra-designer>
```

## 6. How It Works

This interface can only be invoked within the designer. Where needed, all declared global event extensions are retrieved via `Set<GlobalListenerProvider> providers = ExtraDesignClassManager.getInstance().getArray(GlobalListenerProvider.XML_TAG)`.

In the standard product, the main activation point is the `GlobalListenerProviderManager` shown above.

## 7. Limitations

None

## 8. Useful Links

[demo-global-listener-provider](https://code.fanruan.com/hugh/demo-global-listener-provider)

## 9. Open Source Examples

Disclaimer: All open-source examples in the documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide instruction or guidance on any outcomes related to open-source examples. Any commercial use is entirely at the user's own risk.

None available at this time.

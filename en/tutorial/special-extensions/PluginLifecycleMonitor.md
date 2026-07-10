# PluginLifecycleMonitor

| Attribute | Value |
| --- | --- |
| Interface type | lifecycle-monitor |
| Module | extra-plugin-observer |
| Fully qualified class name | `com.fr.plugin.observer.inner.PluginLifecycleMonitor` |

## Background and Use Cases

The `PluginLifecycleMonitor` interface is primarily used to let a plugin listen to its own lifecycle events and perform environment setup and teardown at each stage. A common use case is pre-initializing a complex plugin environment to avoid initialization overhead affecting feature performance when business units become active.

Because this interface is exposed at a coarse granularity, it can also be used in special cases to intercept product execution logic — for example, to register and unregister objects in internal extensible interfaces such as Factory, Provider, Manager, or Register that are not standard plugin extension points.

## Interface Definition

```java
package com.fr.plugin.observer.inner;

import com.fr.plugin.context.PluginContext;
import com.fr.stable.fun.Level;

/**
 * Used by a plugin to listen to its own lifecycle events.
 */
public interface PluginLifecycleMonitor extends Level {

    int CURRENT_LEVEL = 1;

    void afterRun(PluginContext context);

    void beforeStop(PluginContext context);

    void afterInstall(PluginContext context);

    void beforeUninstall(PluginContext context);

    void afterUpdate(PluginContext context);
}
```

## Supported Versions

| Product | Version | Supported |
| --- | --- | --- |
| FR | 9.0 | Yes |
| FR | 10.0 | Yes |
| FR | 11.0 | Yes |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<lifecycle-monitor class="your class name"/>
```

## How It Works

Plugin lifecycle events are provided and triggered by `PluginInnerMonitorTrigger`. When the plugin engine loads a plugin, it fires the corresponding trigger event. `PluginInnerMonitorCreator` then reads the lifecycle monitor interface declarations from the plugin's loading context and activates them.

## Risk Assessment

Because this interface executes at an early stage, it can interfere with product execution logic and carries some degree of intrusiveness and risk. The three scenarios below are listed in increasing order of risk:

**Scenario 1 (Low risk)**: Registering, initializing, and unregistering the plugin's own configuration and execution environment within the lifecycle, without relying on any product-specific functionality. Examples include loading pure configuration data, or dynamically generating and updating resource files. These scenarios have virtually no impact on the product.

**Scenario 2 (Medium risk)**: Using non-standard registerable plugin interfaces provided internally by the product, and registering the plugin's required interface instances during initialization to intercept product execution. This is more intrusive, but since the product is designed to support this type of registration, the risk of breakage is low.

**Scenario 3 (High risk)**: Using reflection, proxying, or other special techniques to replace static members of the product during plugin initialization, in order to intercept execution logic. This approach is both highly intrusive and destructive, and is very likely to cause unpredictable errors across product version upgrades. Use with extreme caution after a thorough risk assessment.

## Related Links

- Demo: [demo-lifecycle-monitor](https://code.fanruan.com/hugh/demo-lifecycle-monitor)

## Open-Source Examples

> Disclaimer: All open-source examples in this documentation are independently developed and provided by third-party developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide instruction or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7866](https://code.fanruan.com/hugh/open-JSD-7866)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7803](https://code.fanruan.com/hugh/open-JSD-7803)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)
- [open-JSD-6890](https://code.fanruan.com/hugh/open-JSD-6890)

# PluginLifecycleMonitor

| 属性 | 值 |
| --- | --- |
| 接口类型 | lifecycle-monitor |
| 所属模块 | extra-plugin-observer |
| 完整类名 | `com.fr.plugin.observer.inner.PluginLifecycleMonitor` |

## 背景与场景

`PluginLifecycleMonitor` 接口主要用于对插件提供自身的生命周期事件的监听，并在不同事件阶段完成环境的准备和卸载工作。常见用法是：对初始化较为复杂的插件环境进行提前初始化，避免业务单元生效时因初始化操作影响功能执行效率。

由于接口开放粒度较大，该接口还可以在特殊情况下对产品执行逻辑进行干预，例如对某些非标准插件接口的 Factory / Provider / Manager / Register 等内部可扩展接口对象进行注册和卸载。

## 接口定义

```java
package com.fr.plugin.observer.inner;

import com.fr.plugin.context.PluginContext;
import com.fr.stable.fun.Level;

/**
 * 用于插件内部监听自己的各个生命周期
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

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<lifecycle-monitor class="your class name"/>
```

## 原理说明

插件整个生命周期事件由 `PluginInnerMonitorTrigger` 触发器提供并监听。当插件被引擎加载时会触发对应的触发器事件，再通过 `PluginInnerMonitorCreator` 从加载插件的上下文环境中读取生命周期监听接口申明并使其生效。

## 风险说明

由于该接口执行时机较早，可以对产品本身的运行逻辑进行干预，存在一定侵入性和破坏性。以下三种场景的风险等级依次递增：

**场景一（低风险）**：对插件自身的配置环境和执行环境在生命周期中进行注册、初始化及卸载操作，不依赖于产品本身的相关功能。例如纯配置信息的加载、资源文件的动态生成和更新等。这类场景对产品几乎没有侵入和破坏。

**场景二（中风险）**：使用产品内部提供的可注册的非标准插件接口，在插件初始化准备时将插件自身所需的接口实例注册进去，从而实现干预产品执行的目的。此类场景具有较强侵入性，但由于产品设计上预留了这类注册接口，破坏性很小。

**场景三（高风险）**：通过反射、代理等特殊手段，在插件初始化时对产品的静态成员进行替换，以干预产品执行逻辑。此类场景具有较强的侵入性和破坏性，在产品版本迭代过程中极有可能导致不可预知的异常，使用时须谨慎评估风险。

## 常用链接

- Demo：[demo-lifecycle-monitor](https://code.fanruan.com/hugh/demo-lifecycle-monitor)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7866](https://code.fanruan.com/hugh/open-JSD-7866)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7803](https://code.fanruan.com/hugh/open-JSD-7803)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)
- [open-JSD-6890](https://code.fanruan.com/hugh/open-JSD-6890)

# ComposeWebResourceProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.ComposeWebResourceProvider` |

## 背景与场景

该接口用于对帆软决策系统和报表展示进行前端定制，常见场景包括：产品配置项不足时的样式和交互调整、前端功能扩展（平台改造、自定义控件、自定义图表等）。

相比 `WebResourceProvider`，`ComposeWebResourceProvider` 支持**一次性引入多个前端组件**，而无需逐一注册。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface ComposeWebResourceProvider extends ComposeWebCoalition {
    String MARK_STRING = "ComposeWebResourceProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
public interface ComposeWebCoalition extends Mutable {
    WebCoalition[] webCoalitions();
}
```

```java
public interface WebCoalition extends Mutable {
    Atom attach();

    @Deprecated
    Atom client();

    Atom[] clients();
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <ComposeWebResourceProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`Registry` 类通过 `AtomActivator` 管理子组件，同时处理 `WebCoalition` 和 `ComposeWebCoalition` 的插件注入。组件通过 `Registry.register()` 注册，通过 `Registry.remove()` 卸载。

## 常用链接

- Demo：[demo-compose-web-resource-provider](https://code.fanruan.com/hugh/demo-compose-web-resource-provider)

# ComposeWebResourceProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.ComposeWebResourceProvider` |

## Background and Use Cases

This interface is used for frontend customization of the FineReport decision system and report rendering. Common scenarios include: style and interaction adjustments when built-in configuration options are insufficient, and frontend feature extensions (platform customization, custom widgets, custom charts, etc.).

Compared to `WebResourceProvider`, `ComposeWebResourceProvider` supports **importing multiple frontend components at once** without registering them individually.

## Interface Definition

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

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <ComposeWebResourceProvider class="your class name"/>
</extra-decision>
```

## How It Works

The `Registry` class manages sub-components via `AtomActivator`, handling plugin injection for both `WebCoalition` and `ComposeWebCoalition`. Components are registered via `Registry.register()` and unregistered via `Registry.remove()`.

## Useful Links

- Demo: [demo-compose-web-resource-provider](https://code.fanruan.com/hugh/demo-compose-web-resource-provider)

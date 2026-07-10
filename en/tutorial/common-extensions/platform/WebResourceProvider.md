# WebResourceProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.WebResourceProvider` |

## Background and Use Cases

This interface is used for frontend customization of the FineReport decision system and report rendering. Applicable scenarios include:

1. Style and interaction adjustments when built-in configuration options are insufficient
2. Frontend feature extensions: platform customization, custom widgets, custom charts, etc.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.WebCoalition;

public interface WebResourceProvider extends WebCoalition {
    String MARK_STRING = "WebResourceProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.stable.fun.mark;

import com.fr.web.struct.Atom;

public interface WebCoalition extends Mutable {
    Atom attach();

    @Deprecated
    Atom client();

    Atom[] clients();
}
```

Common mount points (return values of `attach()`):

| Mount Point | Description |
| --- | --- |
| `CommonComponent.KEY` | Common components |
| `MainComponent.KEY` | Main interface |
| `FormMainComponent.KEY` | Form main interface |
| `LoginComponent.KEY` | Login page |
| `ReportMainComponent.KEY` | Report main interface |

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <WebResourceProvider class="your class name"/>
</extra-decision>
```

## How It Works

Plugin instances are registered to the `Registry` children collection via `AtomActivator#prepare()`. Components that support extension implement `AssembleComponent`, using `Registry.getChildren()` to retrieve extension declarations. The complete dependency chain is loaded via `AtomBuilder.create().buildAssembleFilePath()`.

## Useful Links

- Demo (simple): [demo-web-resource-simple](https://code.fanruan.com/hugh/demo-web-resource-simple)
- Demo (full): [demo-web-resource](https://code.fanruan.com/hugh/demo-web-resource)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-8173](https://code.fanruan.com/hugh/open-JSD-8173)
- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7930](https://code.fanruan.com/hugh/open-JSD-7930)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)

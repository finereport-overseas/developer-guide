# LocaleFinder

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.LocaleFinder` |

## Background and Use Cases

Internationalization (i18n) is an important way to make plugin functionality adapt to multiple language environments simultaneously. The `LocaleFinder` interface is used to register the path of a plugin's i18n resource files, and applies to the following three scenarios:

- Frontend UI display (called via JavaScript)
- Log output
- Designer interface display

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Level;

/**
 * @author richie
 * @date 2015-07-09
 * @since 8.0
 * Interface for locating i18n files within a plugin
 */
public interface LocaleFinder extends Level {

    String MARK_STRING = "LocaleFinder";

    int CURRENT_LEVEL = 1;

    /**
     * Finds the path of the i18n file
     *
     * @return the path of the i18n file
     */
    String find();
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 3.6 | Supported |
| BI | 4.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <LocaleFinder class="your class name"/>
</extra-core>
```

## How It Works

The interface is registered into `InterProviderFactory` via `ExtraClassManager#addLocaleFinder`, and the instance is injected into `InterProvider`. Localized text is retrieved by calling the `getLocText` method on the `InterProvider` instance.

## Useful Links

- Demo: [demo-locale-fineder](https://code.fanruan.com/hugh/demo-locale-fineder)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7803](https://code.fanruan.com/hugh/open-JSD-7803)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)
- [open-JSD-6890](https://code.fanruan.com/hugh/open-JSD-6890)

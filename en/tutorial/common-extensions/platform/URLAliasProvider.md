# URLAliasProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.URLAliasProvider` |

## Background and Use Cases

Earlier FineReport products routed web services using `op` and `cmd` parameters. After version 10.0 introduced the Spring framework, `HttpHandlerProvider` was provided. `URLAliasProvider` is another widely used web service interface that **supports hot reloading** and exposes services through short-path aliases.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.decision.webservice.url.provider.URLAliasRegister;
import com.fr.stable.fun.mark.Mutable;

public interface URLAliasProvider extends URLAliasRegister, Mutable {
    String XML_TAG = "URLAliasProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.decision.webservice.url.provider;

import com.fr.decision.webservice.url.alias.URLAlias;

public interface URLAliasRegister {
    URLAlias[] registerAlias();
}
```

Alias factory methods:

```java
// Create a plugin alias
public static URLAlias createPluginAlias(String aliasPath, String pluginPath) {
    return createPluginAlias(aliasPath, pluginPath, false);
}

public static URLAlias createPluginAlias(String aliasPath, String pluginPath,
        boolean isPublic, boolean wideRange) {
    PluginURLAlias alias = new PluginURLAlias();
    alias.setShortPath(aliasPath);
    alias.setPluginPath(pluginPath);
    alias.setPublicURL(isPublic);
    alias.setWideRange(wideRange);
    return alias;
}
```

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
    <URLAliasProvider class="your class name"/>
</extra-decision>
```

## How It Works

The routing alias module reads all declared request aliases via `URLAliasManager#init` during initialization and stores them in `aliasCollection`. `ShortURLService` provides the entry point: clients send requests to `/url/*`, and `URLAliasManager#resolveAlias(path)` resolves the alias to the original URL.

## Useful Links

- Demo: [demo-url-alias](https://code.fanruan.com/hugh/demo-url-alias)
- Demo: [demo-web-request](https://code.fanruan.com/fanruan/demo-web-request)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-7868](https://code.fanruan.com/hugh/open-JSD-7868)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7660](https://code.fanruan.com/hugh/open-JSD-7660)
- [open-JSD-7639](https://code.fanruan.com/hugh/open-JSD-7639)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7339](https://code.fanruan.com/hugh/open-JSD-7339)

# GlobalRequestFilterProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.GlobalRequestFilterProvider` |

## Background and Use Cases

This interface complements `EmbedRequestFilterProvider` for request pre-processing. It provides **a fully equivalent effect to a Servlet Filter** without requiring `web.xml` configuration. Note: this interface **does not support hot reloading**.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

public interface GlobalRequestFilterProvider extends Mutable,
        Comparable<GlobalRequestFilterProvider> {
    String MARK_STRING = "GlobalRequestFilterProvider";
    int CURRENT_LEVEL = 1;

    String filterName();
    String[] urlPatterns();
    String externalFilterClassName();
    void init(FilterConfig filterConfig) throws ServletException;
    Map<String, String> initializationParameters();
    void doFilter(HttpServletRequest req, HttpServletResponse res,
                  FilterChain filterChain) throws IOException, ServletException;
    void destroy();
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
    <GlobalRequestFilterProvider class="your class name"/>
</extra-decision>
```

## How It Works

`DecisionServletInitializer` reads all implementation instances at startup and registers them into the `ServletContext`.

## Useful Links

- Demo: [demo-global-request-filter](https://code.fanruan.com/hugh/demo-global-request-filter)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-7957](https://code.fanruan.com/hugh/open-JSD-7957)
- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7874](https://code.fanruan.com/hugh/open-JSD-7874)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7492](https://code.fanruan.com/hugh/open-JSD-7492)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)

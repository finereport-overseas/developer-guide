# EmbedRequestFilterProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.EmbedRequestFilterProvider` |

## Background and Use Cases

This interface complements `RequestPreHandleProvider` for request pre-processing. It provides **an effect nearly equivalent to a Servlet Filter (`javax.servlet.Filter`)**, and can be used directly in plugin code without configuring `web.xml`. It also supports hot reloading.

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

public interface EmbedRequestFilterProvider extends Mutable {
    String MARK_STRING = "EmbedRequestFilterProvider";
    int CURRENT_LEVEL = 2;

    void init(FilterConfig filterConfig);

    void filter(HttpServletRequest req, HttpServletResponse res)
            throws IOException, ServletException;

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
    <EmbedRequestFilterProvider class="your class name"/>
</extra-decision>
```

## How It Works

The platform's Servlet initialization module injects a Filter at startup that listens to plugin lifecycle events to dynamically cache `EmbedRequestFilterProvider` instances. They are invoked in sequence when the Filter executes.

## Notes

- Only filters requests to the `/decision` route (internal Servlet)
- Cannot terminate subsequent logic execution (unless an exception is thrown)
- Limited to request pre-processing (header and parameter adjustments); **cannot redirect, forward, or write a response**
- Cannot specify execution order among implementations from different plugins

## Useful Links

- Demo: [demo-embed-request-filter](https://code.fanruan.com/hugh/demo-embed-request-filter)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-7449](https://code.fanruan.com/hugh/open-JSD-7449)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)

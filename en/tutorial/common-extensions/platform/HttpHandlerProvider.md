# HttpHandlerProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.HttpHandlerProvider` |

## Background and Use Cases

Earlier FineReport products routed web services using `op` and `cmd` parameters. After the 10.0 upgrade introduced the Spring framework, the old service interface was gradually superseded. `HttpHandlerProvider` is the new primary web service extension interface and **supports hot reloading**.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface HttpHandlerProvider extends Mutable {
    String XML_TAG = "HttpHandlerProvider";
    int CURRENT_LEVEL = 1;

    HttpHandler[] registerHandlers();
}
```

```java
package com.fr.decision.fun;

import com.fr.third.springframework.web.bind.annotation.RequestMethod;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface HttpHandler {
    RequestMethod getMethod();
    String getPath();
    boolean isPublic();
    void handle(HttpServletRequest req, HttpServletResponse res) throws Exception;
    boolean needAdmin();
    String[] modules();
    boolean accessControl(String userId);
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
    <HttpHandlerProvider class="your class name"/>
</extra-decision>
```

## How It Works

`PluginServiceActivator` reads all declared web interfaces via `PluginServiceManager` during initialization and stores them in `handlerMap`. Requests routed through `/plugin/public/` or `/plugin/private/` are handled by `PluginServiceManager#handleRequest`, which matches them against the stored handler configurations.

**Full URL format**:
```
http(s)://ip:port/webroot/decision/plugin/public|private/$pluginID/$path
```

Access control for private routes is determined by the AND combination of `needAdmin()`, `modules()`, and `accessControl()`. Note: `accessControl()` receives a user ID, not a username.

## Useful Links

- Demo: [demo-http-handler](https://code.fanruan.com/hugh/demo-http-handler)
- Demo: [demo-web-request](https://code.fanruan.com/fanruan/demo-web-request)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7868](https://code.fanruan.com/hugh/open-JSD-7868)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7660](https://code.fanruan.com/hugh/open-JSD-7660)
- [open-JSD-7639](https://code.fanruan.com/hugh/open-JSD-7639)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7339](https://code.fanruan.com/hugh/open-JSD-7339)

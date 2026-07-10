# Service

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.Service` |

## Background and Use Cases

The `Service` interface was the primary extension mechanism in versions 8.0/9.0, and is gradually being replaced by newer interfaces in version 10.0. In version 10.0, its main use case is: **providing web interfaces that follow the report request style**.

## Interface Definition

```java
package com.fr.stable.fun;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * FineReport service for processing HTTP requests
 */
public interface Service {

    String XML_TAG = "WebService";

    /**
     * Returns the OP parameter associated with this service
     *
     * @return the op parameter
     */
    String actionOP();

    /**
     * Processes an HTTP request
     *
     * @param req       the HTTP request
     * @param res       the HTTP response
     * @param op        the op parameter value
     * @param sessionID the session ID of the current report object
     * @throws Exception
     */
    void process(HttpServletRequest req, HttpServletResponse res,
                 String op, String sessionID) throws Exception;
}
```

```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.Service;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public abstract class NoSessionIDService implements Service {
    public void process(HttpServletRequest req, HttpServletResponse res,
                        String op, String sessionID) throws Exception {
        process(req, res, op);
    }

    public abstract void process(HttpServletRequest req, HttpServletResponse res,
                                  String op) throws Exception;
}
```

## Supported Versions

| Product | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported | — |
| FR | 9.0 | Supported | — |
| FR | 10.0 | Supported | Report scene web services only |
| FR | 11.0 | Supported | |
| BI | 3.6 | Supported | — |
| BI | 4.0 | Supported | — |
| BI | 5.1 | Supported | Not recommended |
| BI | 5.1.2 | Supported | Not recommended |
| BI | 5.1.3 | Supported | Not recommended |

## Plugin Registration

Add the following node in `plugin.xml`:

> Note: The registration tag is `<WebService>` (because `XML_TAG = "WebService"`), not `<Service>`.

```xml
<extra-core>
    <WebService class="your class name"/>
</extra-core>
```

## How It Works

Registration is completed through `WebActionsDispatcher` during plugin lifecycle events. After loading, the service is injected into the dispatcher via `ReportDispatcher.addExtraServices`. When a report request (`view/report` or `view/form`) arrives, it is routed to the corresponding service handler based on the `op` parameter value.

## Useful Links

- Demo: [demo-web-service](https://code.fanruan.com/hugh/demo-web-service)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)
- [open-JSD-7866](https://code.fanruan.com/hugh/open-JSD-7866)

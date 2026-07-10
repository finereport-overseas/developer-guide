# RequestPreHandleProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.RequestPreHandleProvider` |

## Background and Use Cases

`RequestPreHandleProvider` is a request pre-processing plugin interface based on Spring's `HandlerInterceptorAdapter`, exposed in early 10.0 versions. It is used for request information supplementation, transformation, and filtering — similar to a Filter but executed after filters.

> **Note**: This interface has been marked as deprecated due to design flaws and is retained only for backward compatibility. New development should use `EmbedRequestFilterProvider` or `GlobalRequestFilterProvider`.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface RequestPreHandleProvider extends Mutable {
    String MARK_STRING = "RequestPreHandleProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Whether the request requires pre-processing
     */
    boolean accept(HttpServletRequest req);

    /**
     * Request pre-processing logic
     */
    boolean preHandle(HttpServletRequest req, HttpServletResponse res);
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
    <RequestPreHandleProvider class="your class name"/>
</extra-decision>
```

## How It Works

The MVC framework initializes an interceptor containing `RequestPreHandleInterceptor`, which invokes the implemented `RequestPreHandleProvider` interface methods.

## Notes

- Only **one implementation instance** executes per request (the first instance where `accept()` returns true takes effect; others are skipped)
- Only intercepts `/decision/*` requests; **excludes** `URLAliasProvider` short links (`/url/**`) and deployment info endpoints (`/v10/deployment/**`)
- This is an interceptor interface; **it cannot be used to expose new web service endpoints**

## Useful Links

- Demo: [demo-request-pre-handler](https://code.fanruan.com/hugh/demo-request-pre-handler)

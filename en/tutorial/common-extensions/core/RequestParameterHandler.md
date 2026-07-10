# RequestParameterHandler

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.RequestParameterHandler` |

## Background and Use Cases

The system retrieves request parameters through the `WebUtils` utility class, which supports parameter parsing, transformation, addition/removal, and permission filtering. `RequestParameterHandler` is primarily used for custom handling of report request parameters.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Immutable;
import javax.servlet.http.HttpServletRequest;

public interface RequestParameterHandler extends Immutable {

    String XML_TAG = "RequestParameterHandler";
    int CURRENT_LEVEL = 1;

    Object getParameterFromRequest(HttpServletRequest req, String name);
    Object getParameterFromRequestInputStream(HttpServletRequest req, String name);
    Object getParameterFromAttribute(HttpServletRequest req, String name);
    Object getParameterFromJSONParameters(HttpServletRequest req, String name);
    Object getParameterFromSession(HttpServletRequest req, String name);
    Object getParameterFromHeader(HttpServletRequest req, String name);
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
    <RequestParameterHandler class="your class name"/>
</extra-core>
```

## How It Works

`WebUtils` is the standard utility class for reading request information and parameters. It obtains the `RequestParameterHandler` instance via `NetworkHelper`; if none is registered, `DefaultRequestParameterHandler` is used.

**Note:** It is recommended to extend `DefaultRequestParameterHandler` rather than `AbstractRequestParameterHandler`, using the adapter pattern to override only the necessary methods. All access to request information must use `WebUtils` or finekit methods to ensure compatibility.

## Useful Links

- Demo: [demo-request-parameter-handler](https://code.fanruan.com/hugh/demo-request-parameter-handler)

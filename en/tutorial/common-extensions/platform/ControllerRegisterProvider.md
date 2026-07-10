# ControllerRegisterProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.ControllerRegisterProvider` |

## Background and Use Cases

Earlier versions (8.0/9.0) used the `Service` interface, and early 10.0 provided `HttpHandlerProvider` — both required developers to write extensive request/response and permission logic. With the introduction of the Spring framework in version 10.0, the official team provided the `ControllerRegisterProvider` interface, which supports **Spring annotation-style web service development** in plugins, greatly simplifying development.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface ControllerRegisterProvider extends Mutable {
    String XML_TAG = "ControllerRegisterProvider";
    int CURRENT_LEVEL = 1;

    Class<?>[] getControllers();
}
```

Controller example:

```java
package com.tptj.demo.hg.controller.register;

import com.fr.decision.webservice.annotation.LoginStatusChecker;
import com.fr.third.springframework.stereotype.Controller;
import com.fr.third.springframework.web.bind.annotation.RequestMapping;
import com.fr.third.springframework.web.bind.annotation.ResponseBody;

@Controller
@LoginStatusChecker(required = false)
public class HelloWorld {
    @RequestMapping(value = "/hello/world")
    @ResponseBody
    public String hello() throws Exception {
        return "Hello World";
    }
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
    <ControllerRegisterProvider class="your class name"/>
</extra-decision>
```

## How It Works

The plugin service module reads all registered instances via `PluginControllerManager#init` and injects them into `SpringControllerHandler` using `registerController`.

## Useful Links

- Demo: [demo-controller-register](https://code.fanruan.com/hugh/demo-controller-register)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-8253](https://code.fanruan.com/hugh/open-JSD-8253)
- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7843](https://code.fanruan.com/hugh/open-JSD-7843)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)

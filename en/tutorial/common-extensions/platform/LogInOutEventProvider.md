# LogInOutEventProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.LogInOutEventProvider` |

## Background and Use Cases

This interface is used to monitor and manage user login/logout behavior. It supports scenarios such as **event listening, message push, and redirecting to a specific login page after logout**.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface LogInOutEventProvider extends Mutable {
    String MARK_STRING = "LogInOutEventProvider";
    int CURRENT_LEVEL = 2;

    void loginAction(LogInOutResultInfo result);

    String logoutAction(LogInOutResultInfo result);
}
```

```java
public class LogInOutResultInfo {
    private HttpServletRequest request;
    private HttpServletResponse response;
    private String username;
    private boolean result;
    // Constructor and getter/setter omitted
}
```

## Supported Versions

| Product Line | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 10.0 | Supported | Requires 10.0.3+; earlier versions use LoginEventProvider |
| FR | 11.0 | Supported | |
| BI | 5.1 | Supported | — |
| BI | 5.1.2 | Supported | — |
| BI | 5.1.3 | Supported | — |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <LogInOutEventProvider class="your class name"/>
</extra-decision>
```

## How It Works

`WebServiceActivator` reads all declared implementations at startup and registers them to the LOGIN/LOGOUT events. They are triggered after `LoginResource` executes the standard login, captcha login, or logout methods.

**Note**: Do not use response redirection in `logoutAction` to implement post-logout navigation — the logout request is an AJAX call, so redirects will not take effect.

## Useful Links

- Demo: [demo-login-out-event-provider](https://code.fanruan.com/hugh/demo-login-out-event-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-7957](https://code.fanruan.com/hugh/open-JSD-7957)
- [open-JSD-7858](https://code.fanruan.com/hugh/open-JSD-7858)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)

# HttpAuthorizeProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.HttpAuthorizeProvider` |

## Background and Use Cases

The platform provides three default authentication methods. HTTP authentication verifies login page credentials through a third-party service. Version 10.0 strengthened security requirements, introducing compatibility issues with 8.0/9.0 implementations. This interface is used to customize HTTP authentication handling. Applicable scenarios include:

- Upgrading HTTP authentication from older versions
- Synchronizing user information for password-less data
- Integrating existing services that return non-standard response formats

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface HttpAuthorizeProvider extends Mutable {
    String MARK_STRING = "HttpAuthorizeProvider";
    int CURRENT_LEVEL = 1;

    Scope scope();

    boolean authorize(String username, String inputPassword,
                      String savedPassword, String hashPassword);

    boolean authorize(String uuid, String returnMessage);

    enum Scope {
        REPLACE,
        CHECK
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
    <HttpAuthorizeProvider class="your class name"/>
</extra-decision>
```

## Notes

- When `scope()` returns `REPLACE`, the provider is executed first, replacing the default authentication logic
- The super-admin account is not affected by this interface

## Useful Links

- Demo: [demo-http-authorize-provider](https://code.fanruan.com/hugh/demo-http-authorize-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [demo-auth-http](https://code.fanruan.com/fanruan/demo-auth-http)

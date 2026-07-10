# AccessProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.AccessProvider` |

## Background and Use Cases

The `AccessProvider` interface is used to extend third-party authentication methods by integrating external identity verification through implicit login. It is primarily intended for login scenarios (not SSO), such as mobile third-party app-triggered login integration or direct calls to third-party authorization APIs for authentication.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface AccessProvider extends Mutable {
    String MARK_STRING = "AccessProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Custom login verification method
     * @param username username
     * @param password password
     * @param ticket   third-party authentication credential
     * @return whether login verification passed
     */
    boolean access(String username, String password, String ticket);

    /**
     * Custom login verification method
     * @param username username
     * @param password password
     * @param ticket   third-party authentication credential
     * @return username
     */
    String auth(String username, String password, String ticket);
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
    <AccessProvider class="your class name"/>
</extra-decision>
```

## How It Works

The platform's login service entry point is `LoginResource`, which exposes a `POST /login/third/auth` endpoint that accepts a `ThirdAuthInfoBean`. This endpoint calls `LoginService#login()` with the credentials and token, then invokes all registered `AccessProvider` instances for authentication verification.

## Useful Links

- Demo: [demo-access-provider](https://code.fanruan.com/hugh/demo-access-provider)

# SessionPrivilegeFilterProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.SessionPrivilegeFilterProvider` |

## Background and Use Cases

In early versions of FineReport, this interface had relatively blurry boundaries and covered multiple scenarios: sessionID brute-force protection, response enhancement, Cookie encryption/decryption, and report request pre-processing. In version 10.0, sessionIDs are more resistant to cracking, response enhancement and Cookie operations have been migrated to other interfaces, and report pre-processing has alternative implementations.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface SessionPrivilegeFilterProvider extends Mutable {
    String XML_TAG = "SessionPrivilegeFilterProvider";
    int CURRENT_LEVEL = 1;

    void addSecurityResponseHeader(HttpServletResponse res);
    void filterSession(HttpServletRequest req, HttpServletResponse res);
    String encodeSessionID(String sessionID);
    String encodeCookie(String cookieStr);
    String decodeCookie(String encodeStr);
}
```

## Supported Versions

| Product | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported | — |
| FR | 9.0 | Supported | — |
| FR | 10.0 | Supported | Only some methods take effect |
| FR | 11.0 | Supported | |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <SessionPrivilegeFilterProvider class="your class name"/>
</extra-core>
```

## How It Works

Implementations are retrieved via `PluginModule.getAgent(PluginModule.ExtraCore).getArray()`. Activated during sessionInfo creation and session operation request dispatching (version 9.0 and earlier).

**Version 10.0 limitations:** Only the following two methods take effect:
- `filterSession()`: Called when session operation requests are dispatched
- `encodeSessionID()`: Called at session creation, before the sessionID is bound

## Useful Links

- Demo: [demo-session-privilege-filter-provider](https://code.fanruan.com/hugh/demo-session-privilege-filter-provider)

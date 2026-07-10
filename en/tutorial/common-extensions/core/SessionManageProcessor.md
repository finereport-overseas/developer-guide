# SessionManageProcessor

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.SessionManageProcessor` |

## Background and Use Cases

After FineReport completes server-side computation, the intermediate objects required for rendering or exporting (including templates, parameters, and results) are stored in a unified `reportSessionInfo` object and mapped by ID. `SessionManageProcessor` allows developers to customize monitoring and processing logic throughout the session object's lifecycle — for example, pre-processing templates/parameters at creation, or managing additional caches at destruction.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Immutable;

public interface SessionManageProcessor extends Immutable {

    String XML_TAG = "SessionManageProcessor";
    int CURRENT_LEVEL = 1;

    /**
     * Records session creation count
     * @param sessionID session id
     */
    void registSessionCreate(String sessionID);

    /**
     * Records session destruction count
     * @param sessionID session id
     */
    void registSesssionDestroy(String sessionID);

    /**
     * Gets the current server session pool status
     */
    String getServerState();
}
```

> Note: The method name `registSesssionDestroy` contains three 's' characters, consistent with the original interface definition.

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <SessionManageProcessor class="your class name"/>
</extra-core>
```

## How It Works

This interface is only called in the designer. The implementation is obtained via:

```java
SessionManageProcessor processor = PluginModule.getAgent(PluginModule.ExtraCore)
    .getSingle(SessionManageProcessor.XML_TAG);
```

This method retrieves the last declared listener among all registered session management interface plugins. The default implementation runs through `SessionPoolManager`.

**Special limitations:**
- The `getServerState()` method is not used in version 10.0
- This interface is exclusive (`Immutable`); ensure there are no conflicting plugins in your environment

## Useful Links

- Demo: [demo-session-manage-processor](https://code.fanruan.com/hugh/demo-session-manage-processor)

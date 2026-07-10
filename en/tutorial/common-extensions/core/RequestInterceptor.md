# RequestInterceptor

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.RequestInterceptor` |

## Background and Use Cases

In early FanRuan products, most web services were routed via `op` and `cmd` parameters. Because different customers require different processing logic for the same service, the `RequestInterceptor` interface was opened to support custom service handling. This interface was widely used in versions 8.0–9.0 and less so in 10.0, but its functionality remains unchanged.

Primary use cases:
- Replace an existing Action class bound to an op/cmd pair
- Extend an existing op service with new cmd services

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.plugin.injectable.SpecialLevel;
import com.fr.stable.StringUtils;
import com.fr.stable.fun.mark.Layer;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.RequestCMDReceiver;

public interface RequestInterceptor extends Mutable, RequestCMDReceiver, Layer {

    String MARK_STRING = SpecialLevel.RequestInterceptor.getTagName();
    int CURRENT_LEVEL = 1;

    class ActionKey {
        private String op;
        private String cmd;
        private String pluginID;

        public static ActionKey create(String op, String cmd, String pluginID) {
            if (pluginID == null) {
                pluginID = StringUtils.EMPTY;
            }
            return new ActionKey(op, cmd, pluginID);
        }

        private ActionKey(String op, String cmd, String pluginID) {
            this.op = op;
            this.cmd = cmd;
            this.pluginID = pluginID;
        }

        public String getOp() { return op; }
        public String getCmd() { return cmd; }
        public String getPluginID() { return pluginID; }

        @Override
        public boolean equals(Object o) { ... }

        @Override
        public int hashCode() { ... }
    }
}
```

```java
package com.fr.stable.fun.impl;

public abstract class AbstractRequestInterceptor extends AbstractProvider
        implements RequestInterceptor {

    public String mark4Provider() {
        return getClass().getName();
    }

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    public void actionCMD(HttpServletRequest req, HttpServletResponse res,
                         String sessionID) throws Exception { }

    @Override
    public void actionCMD(HttpServletRequest req, HttpServletResponse res)
            throws Exception { }

    @Override
    public int layerIndex() {
        return CURRENT_LEVEL;
    }
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

```xml
<extra-core>
    <RequestInterceptor class="your class name" op="" cmd="" pluginId=""/>
</extra-core>
```

**Attribute descriptions:**
- `class`: Fully qualified implementation class name
- `op`: Operation parameter, corresponds to WebService
- `cmd`: Command parameter, corresponds to RequestCMDReceiver
- `pluginId`: Plugin identifier (optional; leave empty if the frontend does not pass a pluginId)

## How It Works

**Registration:** The plugin engine's `ExtraClassManager#mountSpecific` identifies ACTION-type interfaces during plugin loading and injects them into `hackActionMap` via `addHackActionCMD`.

**Activation:** When the CMD request dispatcher matches a command, it dispatches the request to the corresponding interface handler (all three of op/cmd/pluginId must match exactly).

**Special note:** The `op` configuration originates from the `WebService` object that contains the `RequestCMDReceiver` group (the source of cmd). The `pluginId` is special: leave it empty when the frontend does not pass it. When multiple plugins override the same cmd, `pluginId` can be used to specify which plugin's implementation to invoke.

## Useful Links

- Demo: [demo-request-interceptor](https://code.fanruan.com/hugh/demo-request-interceptor)

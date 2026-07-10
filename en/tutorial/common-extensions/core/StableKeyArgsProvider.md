# StableKeyArgsProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.StableKeyArgsProvider` |

## Background and Use Cases

FanRuan Designer includes a remote working directory feature based on RPC communication. Many plugin interfaces may run in a remote directory environment and need to read/write server-side resources. The `StableKeyArgsProvider` interface is provided for this scenario. Compared to HTTP, using RPC is simpler because permission pre-processing is already integrated into the RPC flow.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.module.StableKey;
import com.fr.stable.fun.mark.Mutable;
import java.util.List;

/**
 * Provider for passing parameters through StableKey
 * created by Harrison on 2019/02/21
 */
public interface StableKeyArgsProvider<T> extends Mutable {
    String XML_TAG = "StableKeyArgsProvider";
    int LEVEL = 1;

    StableKey<T> getKey();
    List<T> getArgs();
}
```

```java
package com.fr.workspace.base;

import com.fr.module.BaseStableKey;
import com.fr.module.StableKey;
import com.fr.workspace.pool.WorkRPCRegister;

public interface WorkspaceKey {
    StableKey<WorkRPCRegister<?>> RPC = new BaseStableKey();
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <StableKeyArgsProvider class="your class name"/>
</extra-core>
```

## How It Works

The implementation is registered via `PluginInjectionFilterRegister`. In remote working directory mode, the client serializes the interface method and parameters and sends them to the server. The server deserializes them, executes the method via reflection, and serializes the result back.

**Special limitation:** Currently, this interface only supports RPC extension. The `getKey()` method must return `WorkspaceKey.RPC`. The `getArgs()` method returns `List<WorkRPCRegister>`, constructed using `WorkRPCRegister.wrap()`.

## Useful Links

- Demo: [demo-stable-key-args-provider](https://code.fanruan.com/hugh/demo-stable-key-args-provider)

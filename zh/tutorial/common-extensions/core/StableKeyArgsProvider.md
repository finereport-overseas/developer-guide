# StableKeyArgsProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.StableKeyArgsProvider` |

## 背景与场景

帆软设计器包含基于 RPC 通信的远程工作目录功能。插件的许多接口可能运行在远程目录环境中，需要读写服务端资源。`StableKeyArgsProvider` 接口正是为此场景而开放。相比 HTTP，使用 RPC 更为简便，因为权限预处理已集成在 RPC 流程中。

## 接口定义

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

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <StableKeyArgsProvider class="your class name"/>
</extra-core>
```

## 原理说明

实现通过 `PluginInjectionFilterRegister` 完成注册。在远程工作目录模式下，客户端将接口方法和参数序列化后发送到服务器，服务器反序列化后通过反射执行，再将结果序列化返回。

**特别限制**：目前该接口仅支持 RPC 扩展。`getKey()` 方法必须返回 `WorkspaceKey.RPC`。`getArgs()` 方法返回 `List<WorkRPCRegister>`，使用 `WorkRPCRegister.wrap()` 构造。

## 常用链接

- Demo：[demo-stable-key-args-provider](https://code.fanruan.com/hugh/demo-stable-key-args-provider)

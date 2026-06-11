# SessionManageProcessor

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.SessionManageProcessor` |

## 背景与场景

FineReport 后端计算完成后，渲染或导出所需的中间对象（包括模板、参数、结果等）会保存到统一的 `reportSessionInfo` 对象中，并通过 ID 进行映射。`SessionManageProcessor` 允许开发者在 Session 对象的生命周期中自定义监控和处理逻辑，例如：在创建时预处理模板/参数，或在销毁时管理额外的缓存。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Immutable;

public interface SessionManageProcessor extends Immutable {

    String XML_TAG = "SessionManageProcessor";
    int CURRENT_LEVEL = 1;

    /**
     * 记录 Session 创建次数
     * @param sessionID session id
     */
    void registSessionCreate(String sessionID);

    /**
     * 记录 Session 销毁次数
     * @param sessionID session id
     */
    void registSesssionDestroy(String sessionID);

    /**
     * 获取当前服务器 Session 池状态
     */
    String getServerState();
}
```

> 注意：`registSesssionDestroy` 方法名中存在三个 's'，与原始接口定义保持一致。

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <SessionManageProcessor class="your class name"/>
</extra-core>
```

## 原理说明

该接口仅在设计器中调用。通过以下方式获取实现：

```java
SessionManageProcessor processor = PluginModule.getAgent(PluginModule.ExtraCore)
    .getSingle(SessionManageProcessor.XML_TAG);
```

该方法获取所有已注册 Session 管理接口插件中，最后一个声明的监听器。默认实现通过 `SessionPoolManager` 运行。

**特别限制**：
- `getServerState()` 方法在 10.0 版本中未使用
- 该接口为排他性接口（`Immutable`），请确认环境中不存在冲突插件

## 常用链接

- Demo：[demo-session-manage-processor](https://code.fanruan.com/hugh/demo-session-manage-processor)

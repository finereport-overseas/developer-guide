# RequestInterceptor

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.RequestInterceptor` |

## 背景与场景

早期帆软产品的大多数 Web 服务通过 `op` 和 `cmd` 参数进行路由。由于客户需求各异，同一服务有时需要不同的处理逻辑，因此开放了 `RequestInterceptor` 接口，支持自定义服务处理。该接口在 8.0-9.0 版本中使用较多，10.0 版本使用较少，但功能保持不变。

主要用途：
- 替换已有 op/cmd 绑定的 Action 类
- 为已有 op 服务扩展新的 cmd 服务

## 接口定义

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

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 | — |
| FR | 9.0 | 支持 | — |
| FR | 10.0 | 支持 | 仅用于报表场景的 Web 服务 |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 | — |
| BI | 4.0 | 支持 | — |
| BI | 5.1 | 支持 | 不推荐 |
| BI | 5.1.2 | 支持 | 不推荐 |
| BI | 5.1.3 | 支持 | 不推荐 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <RequestInterceptor class="your class name" op="" cmd="" pluginId=""/>
</extra-core>
```

**属性说明：**
- `class`：实现类全名
- `op`：操作参数，对应 WebService
- `cmd`：命令参数，对应 RequestCMDReceiver
- `pluginId`：插件标识（可选；若前端不传递 pluginId，则留空）

## 原理说明

**注册逻辑**：插件引擎的 `ExtraClassManager#mountSpecific` 在插件加载时识别 ACTION 类型接口，通过 `addHackActionCMD` 注入到 `hackActionMap`。

**激活逻辑**：CMD 请求分发器在匹配到对应命令时，将请求分发给对应接口处理器（op/cmd/pluginId 三者完全匹配才生效）。

**特别说明**：`op` 配置来源于包含 `RequestCMDReceiver` 分组（cmd 来源）的 `WebService` 对象。`pluginId` 较为特殊：当前端不传递时，请留空。多个插件覆盖同一 cmd 时，可通过 `pluginId` 指定调用哪个插件的实现。

## 常用链接

- Demo：[demo-request-interceptor](https://code.fanruan.com/hugh/demo-request-interceptor)

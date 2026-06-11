# Service

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.Service` |

## 背景与场景

`Service` 接口是 8.0/9.0 版本中的主要扩展机制，在 10.0 版本逐步被更新的接口所替代。在 10.0 版本中，其主要使用场景为：**提供符合报表请求风格的 Web 接口**。

## 接口定义

```java
package com.fr.stable.fun;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * FineReport对于HTTP请求做处理的服务
 */
public interface Service {

    String XML_TAG = "WebService";

    /**
     * 返回该服务所附带的OP参数
     *
     * @return op参数
     */
    String actionOP();

    /**
     * 处理HTTP请求
     *
     * @param req       HTTP请求
     * @param res       HTTP响应
     * @param op        op参数值
     * @param sessionID 当前广义报表对象的会话ID
     * @throws Exception
     */
    void process(HttpServletRequest req, HttpServletResponse res,
                 String op, String sessionID) throws Exception;
}
```

```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.Service;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public abstract class NoSessionIDService implements Service {
    public void process(HttpServletRequest req, HttpServletResponse res,
                        String op, String sessionID) throws Exception {
        process(req, res, op);
    }

    public abstract void process(HttpServletRequest req, HttpServletResponse res,
                                  String op) throws Exception;
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

> 注意：注册标签为 `<WebService>`（`XML_TAG = "WebService"`），而非 `<Service>`。

```xml
<extra-core>
    <WebService class="your class name"/>
</extra-core>
```

## 原理说明

注册在插件生命周期事件中通过 `WebActionsDispatcher` 完成。加载后，服务通过 `ReportDispatcher.addExtraServices` 注入到分发器。报表请求（`view/report` 或 `view/form`）到达时，根据 `op` 参数值路由到对应的服务处理器。

## 常用链接

- Demo：[demo-web-service](https://code.fanruan.com/hugh/demo-web-service)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)
- [open-JSD-7866](https://code.fanruan.com/hugh/open-JSD-7866)

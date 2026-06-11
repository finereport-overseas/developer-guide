# HttpHandlerProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.HttpHandlerProvider` |

## 背景与场景

早期帆软产品通过 `op` 和 `cmd` 参数进行 Web 服务路由，10.0 升级引入 Spring 框架后，旧版服务接口逐步被替代。`HttpHandlerProvider` 作为新的主要 Web 服务扩展接口，**支持热重载**。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface HttpHandlerProvider extends Mutable {
    String XML_TAG = "HttpHandlerProvider";
    int CURRENT_LEVEL = 1;

    HttpHandler[] registerHandlers();
}
```

```java
package com.fr.decision.fun;

import com.fr.third.springframework.web.bind.annotation.RequestMethod;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface HttpHandler {
    RequestMethod getMethod();
    String getPath();
    boolean isPublic();
    void handle(HttpServletRequest req, HttpServletResponse res) throws Exception;
    boolean needAdmin();
    String[] modules();
    boolean accessControl(String userId);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <HttpHandlerProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`PluginServiceActivator` 初始化时通过 `PluginServiceManager` 读取所有声明的 Web 接口，存入 `handlerMap`。通过 `/plugin/public/` 或 `/plugin/private/` 路由的请求由 `PluginServiceManager#handleRequest` 处理，与存储的处理器配置进行匹配。

**完整 URL 格式**：
```
http(s)://ip:port/webroot/decision/plugin/public|private/$pluginID/$path
```

私有路由的权限校验通过 `needAdmin()`、`modules()` 和 `accessControl()` 三者 AND 逻辑联合判断。注意：`accessControl()` 接收的是用户 ID，而非用户名。

## 常用链接

- Demo：[demo-http-handler](https://code.fanruan.com/hugh/demo-http-handler)
- Demo：[demo-web-request](https://code.fanruan.com/fanruan/demo-web-request)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7868](https://code.fanruan.com/hugh/open-JSD-7868)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7660](https://code.fanruan.com/hugh/open-JSD-7660)
- [open-JSD-7639](https://code.fanruan.com/hugh/open-JSD-7639)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7339](https://code.fanruan.com/hugh/open-JSD-7339)

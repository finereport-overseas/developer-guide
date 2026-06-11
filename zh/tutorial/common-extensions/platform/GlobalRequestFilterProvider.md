# GlobalRequestFilterProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.GlobalRequestFilterProvider` |

## 背景与场景

该接口作为 `EmbedRequestFilterProvider` 的补充，用于请求预处理。它提供了**完全等同于 Servlet Filter 的效果**，无需配置 `web.xml`。注意：该接口**不支持热重载**。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

public interface GlobalRequestFilterProvider extends Mutable,
        Comparable<GlobalRequestFilterProvider> {
    String MARK_STRING = "GlobalRequestFilterProvider";
    int CURRENT_LEVEL = 1;

    String filterName();
    String[] urlPatterns();
    String externalFilterClassName();
    void init(FilterConfig filterConfig) throws ServletException;
    Map<String, String> initializationParameters();
    void doFilter(HttpServletRequest req, HttpServletResponse res,
                  FilterChain filterChain) throws IOException, ServletException;
    void destroy();
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
    <GlobalRequestFilterProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`DecisionServletInitializer` 在启动时读取所有实现实例，并将其注册到 `ServletContext` 中。

## 常用链接

- Demo：[demo-global-request-filter](https://code.fanruan.com/hugh/demo-global-request-filter)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7957](https://code.fanruan.com/hugh/open-JSD-7957)
- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7874](https://code.fanruan.com/hugh/open-JSD-7874)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7492](https://code.fanruan.com/hugh/open-JSD-7492)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)

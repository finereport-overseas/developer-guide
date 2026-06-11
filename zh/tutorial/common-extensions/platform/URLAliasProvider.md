# URLAliasProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.URLAliasProvider` |

## 背景与场景

早期帆软产品通过 `op` 和 `cmd` 参数进行 Web 服务路由，10.0 引入 Spring 框架后提供了 `HttpHandlerProvider`。`URLAliasProvider` 是另一种被广泛使用的 Web 服务接口，**支持热重载**，通过短路径别名对外暴露服务。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.decision.webservice.url.provider.URLAliasRegister;
import com.fr.stable.fun.mark.Mutable;

public interface URLAliasProvider extends URLAliasRegister, Mutable {
    String XML_TAG = "URLAliasProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.decision.webservice.url.provider;

import com.fr.decision.webservice.url.alias.URLAlias;

public interface URLAliasRegister {
    URLAlias[] registerAlias();
}
```

创建别名工厂方法：

```java
// 创建插件别名
public static URLAlias createPluginAlias(String aliasPath, String pluginPath) {
    return createPluginAlias(aliasPath, pluginPath, false);
}

public static URLAlias createPluginAlias(String aliasPath, String pluginPath,
        boolean isPublic, boolean wideRange) {
    PluginURLAlias alias = new PluginURLAlias();
    alias.setShortPath(aliasPath);
    alias.setPluginPath(pluginPath);
    alias.setPublicURL(isPublic);
    alias.setWideRange(wideRange);
    return alias;
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
    <URLAliasProvider class="your class name"/>
</extra-decision>
```

## 原理说明

路由别名模块初始化时通过 `URLAliasManager#init` 读取所有声明的请求别名并存入 `aliasCollection`。`ShortURLService` 提供入口点，客户端通过 `/url/*` 请求，由 `URLAliasManager#resolveAlias(path)` 将别名解析为原始连接。

## 常用链接

- Demo：[demo-url-alias](https://code.fanruan.com/hugh/demo-url-alias)
- Demo：[demo-web-request](https://code.fanruan.com/fanruan/demo-web-request)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7868](https://code.fanruan.com/hugh/open-JSD-7868)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7660](https://code.fanruan.com/hugh/open-JSD-7660)
- [open-JSD-7639](https://code.fanruan.com/hugh/open-JSD-7639)
- [open-JSD-7546](https://code.fanruan.com/hugh/open-JSD-7546)
- [open-JSD-7339](https://code.fanruan.com/hugh/open-JSD-7339)

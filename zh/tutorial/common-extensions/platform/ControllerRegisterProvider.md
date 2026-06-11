# ControllerRegisterProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.ControllerRegisterProvider` |

## 背景与场景

早期版本（8.0/9.0）使用 `Service` 接口，10.0 早期提供了 `HttpHandlerProvider`，两者都需要开发者编写大量请求/响应和权限逻辑。由于 10.0 引入了 Spring 框架，官方团队提供了 `ControllerRegisterProvider` 接口，支持在插件中使用 **Spring 注解风格开发 Web 服务**，大幅简化开发工作。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface ControllerRegisterProvider extends Mutable {
    String XML_TAG = "ControllerRegisterProvider";
    int CURRENT_LEVEL = 1;

    Class<?>[] getControllers();
}
```

Controller 示例：

```java
package com.tptj.demo.hg.controller.register;

import com.fr.decision.webservice.annotation.LoginStatusChecker;
import com.fr.third.springframework.stereotype.Controller;
import com.fr.third.springframework.web.bind.annotation.RequestMapping;
import com.fr.third.springframework.web.bind.annotation.ResponseBody;

@Controller
@LoginStatusChecker(required = false)
public class HelloWorld {
    @RequestMapping(value = "/hello/world")
    @ResponseBody
    public String hello() throws Exception {
        return "Hello World";
    }
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
    <ControllerRegisterProvider class="your class name"/>
</extra-decision>
```

## 原理说明

插件服务模块通过 `PluginControllerManager#init` 读取所有已注册实例，通过 `registerController` 将其注入到 `SpringControllerHandler` 中。

## 常用链接

- Demo：[demo-controller-register](https://code.fanruan.com/hugh/demo-controller-register)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8253](https://code.fanruan.com/hugh/open-JSD-8253)
- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7843](https://code.fanruan.com/hugh/open-JSD-7843)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)

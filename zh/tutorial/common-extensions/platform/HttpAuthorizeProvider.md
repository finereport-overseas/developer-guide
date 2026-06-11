# HttpAuthorizeProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.HttpAuthorizeProvider` |

## 背景与场景

平台提供三种默认认证方式，HTTP 认证通过第三方服务校验登录页凭据。10.0 版本加强了安全要求，与 8.0/9.0 的实现存在兼容性问题。该接口用于自定义 HTTP 认证处理，适用场景：

- 从旧版本升级 HTTP 认证
- 同步无密码数据的用户信息
- 集成非标准返回格式的已有服务

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface HttpAuthorizeProvider extends Mutable {
    String MARK_STRING = "HttpAuthorizeProvider";
    int CURRENT_LEVEL = 1;

    Scope scope();

    boolean authorize(String username, String inputPassword,
                      String savedPassword, String hashPassword);

    boolean authorize(String uuid, String returnMessage);

    enum Scope {
        REPLACE,
        CHECK
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
    <HttpAuthorizeProvider class="your class name"/>
</extra-decision>
```

## 注意事项

- `scope()` 返回 `REPLACE` 时优先执行，替换默认认证逻辑
- 超管账号不受该接口影响

## 常用链接

- Demo：[demo-http-authorize-provider](https://code.fanruan.com/hugh/demo-http-authorize-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [demo-auth-http](https://code.fanruan.com/fanruan/demo-auth-http)

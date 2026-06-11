# SystemOptionProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.SystemOptionProvider` |

## 背景与场景

决策平台管理系统提供了大量管理操作功能。`SystemOptionProvider` 允许开发者扩展自定义管理菜单项，属于功能性接口（而非业务性接口），适用于所有与特定业务逻辑无关的平台扩展场景。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.WebCoalition;

/**
 * 决策平台管理系统菜单扩展项
 */
public interface SystemOptionProvider extends WebCoalition {

    String XML_TAG = "SystemOptionProvider";
    int CURRENT_LEVEL = 1;

    String id();
    String parentId();
    String fullPath();
    String displayName();
    int sortIndex();
}
```

前端菜单注册示例：

```javascript
BI.config("dec.constant.management.navigation", function (items) {
    items.push({
        value: "地址栏的hash值",
        id: "菜单后台模块ID",
        pId: "父节点ID，根节点下可不传",
        text: "菜单显示的文本",
        cardType: "菜单对应的主界面的组件类型",
        cls: "management-directory-font"
    });
    return items;
});
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
    <SystemOptionProvider class="your class name"/>
</extra-decision>
```

## 原理说明

后端管理权限与前端页面声明分离实现。后端管理权限，前端声明管理页面。

## 注意事项

- 后端 `id`/`parentId` 与前端 `id`/`pId` 必须**完全匹配**，否则会导致权限问题
- `fullPath` 用于后端权限校验，格式：`root-_-parent`
- 仅数据连接和智能运营支持子节点扩展，扩展其他功能有破坏原有功能的风险
- 通常需要配合前端资源接口、数据库访问提供者、服务接口及国际化接口一起使用

## 常用链接

- Demo：[demo-system-option-provider](https://code.fanruan.com/hugh/demo-system-option-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [demo-system-management](https://code.fanruan.com/fanruan/demo-system-management)

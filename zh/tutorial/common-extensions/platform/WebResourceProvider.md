# WebResourceProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.WebResourceProvider` |

## 背景与场景

该接口用于对帆软决策系统和报表展示进行前端定制，适用于以下场景：

1. 产品内置配置项不足时，对样式和交互进行调整
2. 前端功能扩展：平台改造、自定义控件、自定义图表等

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.WebCoalition;

public interface WebResourceProvider extends WebCoalition {
    String MARK_STRING = "WebResourceProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.stable.fun.mark;

import com.fr.web.struct.Atom;

public interface WebCoalition extends Mutable {
    Atom attach();

    @Deprecated
    Atom client();

    Atom[] clients();
}
```

常用挂载点（`attach()` 返回值）：

| 挂载点 | 说明 |
| --- | --- |
| `CommonComponent.KEY` | 通用组件 |
| `MainComponent.KEY` | 主界面 |
| `FormMainComponent.KEY` | 表单主界面 |
| `LoginComponent.KEY` | 登录页 |
| `ReportMainComponent.KEY` | 报表主界面 |

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
    <WebResourceProvider class="your class name"/>
</extra-decision>
```

## 原理说明

插件实例通过 `AtomActivator#prepare()` 注册到 `Registry` 的 children 集合。支持扩展的组件实现 `AssembleComponent` 方法，使用 `Registry.getChildren()` 获取扩展声明，完整依赖关系通过 `AtomBuilder.create().buildAssembleFilePath()` 加载。

## 常用链接

- Demo（简单版）：[demo-web-resource-simple](https://code.fanruan.com/hugh/demo-web-resource-simple)
- Demo（完整版）：[demo-web-resource](https://code.fanruan.com/hugh/demo-web-resource)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8173](https://code.fanruan.com/hugh/open-JSD-8173)
- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7930](https://code.fanruan.com/hugh/open-JSD-7930)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)

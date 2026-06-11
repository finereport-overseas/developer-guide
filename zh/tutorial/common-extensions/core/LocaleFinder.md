# LocaleFinder

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.LocaleFinder` |

## 背景与场景

国际化（i18n）是使插件功能同时适应多种语言环境的重要途径。`LocaleFinder` 接口用于注册插件的国际化资源文件路径，适用于以下三种场景：

- 前端 UI 显示（通过 JavaScript 调用）
- 日志输出
- 设计器界面展示

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Level;

/**
 * @author richie
 * @date 2015-07-09
 * @since 8.0
 * 插件中的国际化文件寻找接口
 */
public interface LocaleFinder extends Level {

    String MARK_STRING = "LocaleFinder";

    int CURRENT_LEVEL = 1;

    /**
     * 查找国际化文件的路径
     *
     * @return 国际化文件的路径
     */
    String find();
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |
| BI | 4.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <LocaleFinder class="your class name"/>
</extra-core>
```

## 原理说明

接口通过 `ExtraClassManager#addLocaleFinder` 注册到 `InterProviderFactory`，并将实例注入 `InterProvider`。国际化文本通过调用 `InterProvider` 实例的 `getLocText` 方法获取。

## 常用链接

- Demo：[demo-locale-fineder](https://code.fanruan.com/hugh/demo-locale-fineder)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7944](https://code.fanruan.com/hugh/open-JSD-7944)
- [open-JSD-7814](https://code.fanruan.com/hugh/open-JSD-7814)
- [open-JSD-7803](https://code.fanruan.com/hugh/open-JSD-7803)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
- [open-JSD-6952](https://code.fanruan.com/hugh/open-JSD-6952)
- [open-JSD-6890](https://code.fanruan.com/hugh/open-JSD-6890)

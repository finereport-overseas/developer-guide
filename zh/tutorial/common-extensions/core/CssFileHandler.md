# CssFileHandler

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core / extra-form / extra-report |
| 完整类名 | `com.fr.stable.fun.CssFileHandler` |

## 接口作用

引入额外的 CSS 文件。

> **注意：** 该接口仅用于兼容低版本产品（FR 10.0 以下）中的前端引入实现，且只对报表预览（cpt/frm 模板，不含移动端和 H5 预览）生效，决策平台中已无效。新插件请勿使用。

## 接口内容

### 主要接口

```java
package com.fr.stable.fun;

public interface CssFileHandler extends WebFileHandler {

    String XML_TAG = "CssFileHandler";

    int CURRENT_LEVEL = 1;
}
```

### 关联接口

`WebFileHandler`（父接口）：

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface WebFileHandler extends Mutable {

    String ENCODE_SEPARATOR = "?encode=";

    /**
     * 返回Web端需要的文件的相对路径
     *
     * @return 文件的路径组成的集合
     */
    String[] pathsForFiles();

    String encode();
}
```

- `pathsForFiles()`：返回 CSS 文件在插件 jar 中的相对路径，如 `com/demo/css/main.css`
- `encode()`：已有默认实现，返回资源编码方式，默认值为 `UTF-8`

### 接口接入

在 10.0 版本中以下三种注册方式均只对报表预览生效，生效优先级：`form > report > core`。

```xml
<extra-core>
    <CssFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-core>
```

```xml
<extra-form>
    <CssFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-form>
```

```xml
<extra-report>
    <CssFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-report>
```

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 | |
| FR | 9.0 | 支持 | |
| FR | 10.0 | 支持 | **⚠️ 仅做兼容使用，新插件请勿使用！** |
| FR | 11.0 | 支持 | |
| BI | 3.6 | 支持 | |
| BI | 4.0 | 支持 | |
| BI | 5.1 | 支持 | **⚠️ 仅做兼容使用，新插件请勿使用！** |
| BI | 5.1.2 | 支持 | **⚠️ 仅做兼容使用，新插件请勿使用！** |
| BI | 5.1.3 | 支持 | **⚠️ 仅做兼容使用，新插件请勿使用！** |

## 原理说明

**注册逻辑：** 产品的服务器/报表/决策报表模块（`ServerModule`、`EngineModule`、`FormModule`）启动时（`module.start()`）会从插件中分类读取所申明的 CSS 资源。

**生效逻辑：** `com.fr.web.ResourceHelper#forceInitStyleCache` 执行时，会将插件接口指向的 CSS 文件内容读取并注入到报表预览的主体 CSS 中（区别于 Web 组件接口：组件接口是独立的 CSS 文件加载）。

## 接口示例

- Demo：[demo-css-file-handler](https://code.fanruan.com/hugh/demo-css-file-handler)
- 相关接口：`JavaScriptFileHandler`（三组常见引入 JS 和 CSS 的插件接口对比）

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)

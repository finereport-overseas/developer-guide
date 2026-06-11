# FileContentEditorProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.FileContentEditorProvider` |

## 背景与场景

该接口用于修改帆软产品中缺少标准插件接口的前端功能，常见应用场景包括：图表 JS 修改、控件 UI 调整、业务埋点注入等。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.web.file.RequestFileGroup;
import com.fr.web.file.RequestFileSource;

public interface FileContentEditorProvider extends Mutable {

    String MARK_STRING = "FileContentEditorProvider";
    int CURRENT_LEVEL = 1;

    boolean accept(RequestFileSource fileSource);
    String modify(RequestFileSource fileSource);
    boolean accept(RequestFileGroup fileGroup);
    String compose(RequestFileGroup fileGroup, String separator);
}
```

- `RequestFileSource`：包含 content、path 和 HttpServletRequest 属性，提供 getter/setter 方法和静态工厂方法 `build`
- `RequestFileGroup`：管理 `RequestFileSource` 对象列表，提供获取文件来源、检查格式存在及拼接内容等方法

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <FileContentEditorProvider class="your class name"/>
</extra-core>
```

## 原理说明

该接口拦截资源文件请求（`op=emb`、`op=resource`、`/file` 路径），通过 `PluginModule` 获取注册的提供者实例，实现对 JS/CSS 内容的修改。

## 常用链接

- Demo：[demo-file-content-editor-provider](https://code.fanruan.com/hugh/demo-file-content-editor-provider)

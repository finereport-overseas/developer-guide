# AttachmentDownloader

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.AttachmentDownloader` |

## 背景与场景

FineReport 产品涉及大量文件上传与下载操作。`AttachmentDownloader` 接口允许开发者自定义附件的下载逻辑。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Selectable;

public interface AttachmentDownloader extends Selectable {
    String XML_TAG = "AttachmentDownloader";
    int CURRENT_LEVEL = 1;

    void download(HttpServletRequest req, HttpServletResponse res,
                  String id, String[] idArr) throws Exception;

    String createDownloadScript(String downloadURL);
}
```

`Selectable` 父接口要求实现：
```java
Selector selector();
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <AttachmentDownloader class="your class name"/>
</extra-core>
```

## 原理说明

系统通过 `ExtraClassManager.getInstance().getArray(AttachmentDownloader.XML_TAG)` 获取所有注册实现。请求处理时，选择器（`Selector`）依次对各实现进行判断，第一个匹配的实现生效；若无匹配，则使用默认处理器。

## 注意事项

- 请求参数 `id` 用于传递附件标识
- 可注册多个实现，但每次下载请求只有一个实现通过选择器生效
- `createDownloadScript()` 方法仅对单元格计算所得的附件生效（不适用于上传后的文件），且该方法中只应使用 `DefaultAttachmentDownloader`，避免冲突

## 常用链接

- Demo：[demo-attachment-downloader](https://code.fanruan.com/hugh/demo-attachment-downloader)

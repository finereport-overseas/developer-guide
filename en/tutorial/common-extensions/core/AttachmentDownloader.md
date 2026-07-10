# AttachmentDownloader

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.AttachmentDownloader` |

## Background and Use Cases

FineReport involves extensive file upload and download operations. The `AttachmentDownloader` interface allows developers to customize the download logic for attachments.

## Interface Definition

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

The `Selectable` parent interface requires implementing:
```java
Selector selector();
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <AttachmentDownloader class="your class name"/>
</extra-core>
```

## How It Works

The system retrieves all registered implementations via `ExtraClassManager.getInstance().getArray(AttachmentDownloader.XML_TAG)`. When processing a request, the selector (`Selector`) evaluates each implementation in order; the first matching implementation takes effect. If no match is found, the default handler is used.

## Notes

- The request parameter `id` is used to pass the attachment identifier
- Multiple implementations can be registered, but only one implementation takes effect per download request (determined by the selector)
- The `createDownloadScript()` method only applies to attachments computed by cell formulas (not files that have been uploaded), and only `DefaultAttachmentDownloader` should be used within this method to avoid conflicts

## Useful Links

- Demo: [demo-attachment-downloader](https://code.fanruan.com/hugh/demo-attachment-downloader)

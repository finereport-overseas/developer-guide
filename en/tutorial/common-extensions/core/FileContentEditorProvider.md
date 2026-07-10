# FileContentEditorProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.FileContentEditorProvider` |

## Background and Use Cases

This interface is used to modify frontend functionality in FanRuan products that lacks standard plugin interfaces. Common use cases include: chart JS modifications, widget UI adjustments, and business event tracking injection.

## Interface Definition

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

- `RequestFileSource`: Contains `content`, `path`, and `HttpServletRequest` attributes, with getter/setter methods and a static factory method `build`
- `RequestFileGroup`: Manages a list of `RequestFileSource` objects, providing methods to retrieve file sources, check format availability, and concatenate content

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <FileContentEditorProvider class="your class name"/>
</extra-core>
```

## How It Works

This interface intercepts resource file requests (`op=emb`, `op=resource`, `/file` paths). It retrieves registered provider instances via `PluginModule` to apply modifications to JS/CSS content.

## Useful Links

- Demo: [demo-file-content-editor-provider](https://code.fanruan.com/hugh/demo-file-content-editor-provider)

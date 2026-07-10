# IOFileAttrMark

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.IOFileAttrMark` |

## Background and Use Cases

FanRuan Designer includes many interaction extension interfaces (toolbars, menus, cell right-click menus, property/event configuration panels, etc.). Some interfaces include serialization methods such as `readXML` and `writeXML`, while others do not. The `IOFileAttrMark` interface extends the read/write capability of report configurations, and is typically used in combination with other designer interaction interfaces rather than standalone.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.json.JSONException;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.xml.XMLable;

/**
 * Created by richie on 16/4/19.
 * Provides extra attributes for IOFile objects
 */
public interface IOFileAttrMark extends Mutable, XMLable {

    String MARK_STRING = "IOFileAttrMark";
    int CURRENT_LEVEL = 1;

    /**
     * The XML tag for this attribute, must be unique
     * @return XML tag
     */
    String xmlTag();

    /**
     * Converts the attribute to JSON
     * @return the attribute in JSON format
     */
    JSONObject createJSONConfig() throws JSONException;

    /**
     * Clones the current object
     * @return the cloned object
     */
    IOFileAttrMark clone();
}
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
    <IOFileAttrMark class="your class name"/>
</extra-core>
```

## How It Works

The interface retrieves all declared report attribute extensions via:

```java
Set<IOFileAttrMark> attrs = PluginModule.getAgent(PluginModule.ExtraCore)
    .getArray(IOFileAttrMark.MARK_STRING);
```

During template deserialization, `IOFileAttrMarkReader.read` is responsible for loading extension attributes. The `IOFileAttrMarkManager.getAllAsMap` method loads all plugin-declared attributes via `getExtraMarks`.

## Useful Links

- Demo: [demo-menu-handler (attr-mark branch)](https://code.fanruan.com/hugh/demo-menu-handler/src/branch/10.0/src/main/java/com/tptj/demo/hg/menu/handler/DemoAttrMark.java)

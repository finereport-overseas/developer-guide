# TransferDataSetManagerProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.TransferDataSetManagerProvider` |

## Background and Use Cases

FineReport traditionally requires local design before deployment to a server. For users without remote design capabilities, managing data connections and server-side datasets involves repetitive manual operations. Version 10.0 introduced dataset import/export functionality, and this interface enables custom dataset plugins to also support import/export.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface TransferDataSetManagerProvider<T extends TableData>
        extends Mutable, TransferDataSetManager<T> {

    String XML_TAG = "TransferDataSetManagerProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.decision.webservice.v10.datasource.dataset.manage;

public interface TransferDataSetManager<T extends TableData> {

    Class<? extends TableData> getDataSetClass();

    String serialize(T t);

    T deserialize(String str);

    String[] getDependencyPaths(T t);
}
```

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <TransferDataSetManagerProvider class="your class name"/>
</extra-decision>
```

## How It Works

`TransferDataSetFactory` maintains a registry mapping dataset classes to their managers. For serialization, you can reuse existing `readXML`/`writeXML` implementations:

```java
GeneralXMLTools.writeXMLableAsString(ds)
GeneralXMLTools.readStringAsXMLable(config, ds)
```

The `getDependencyPaths` method returns only the file paths required by the dataset's dependencies during export.

## Useful Links

- Demo: [demo-transfer-data-set-manager-provider](https://code.fanruan.com/hugh/demo-transfer-data-set-manager-provider)

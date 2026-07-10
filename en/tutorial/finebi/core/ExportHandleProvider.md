# ExportHandleProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402895) |

---

## Purpose

Use this interface to inject custom operations before an export action, such as applying custom encryption to the exported file.

**⚠️ Note: This interface operates on a stream. Data volumes can be large — avoid loading data into memory, as doing so risks crashing the server.**

## Interface Content

### Main Interface

```java
package com.finebi.dashboard.impl.service.export;

import com.finebi.dashboard.api.service.export.ExportContext;
import com.fr.stable.fun.mark.Mutable;

import java.io.OutputStream;

/**
 * Provides an interface for modifying the export stream.
 * Created by Hiram on 2018/11/14.
 */
public interface ExportHandleProvider extends Mutable {
    String XML_TAG = "ExportHandleProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Modifies the export stream
     *
     * @param originalOutputStream original export stream
     * @param type                 export type
     * @return processed stream
     */
    OutputStream handleStream(OutputStream originalOutputStream, ExportType type);

    /**
     * Modifies the export stream
     *
     * @param originalOutputStream original export stream
     * @param exportContext        export context
     * @return
     */
    OutputStream handleStream(OutputStream originalOutputStream, ExportContext exportContext);
}
```

## Registration

```xml
<extra-core>
    <ExportHandleProvider class="fully qualified class name of your plugin implementation"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
package com.finebi.provider.api.export;

import com.finebi.dashboard.impl.service.export.ExportHandleProvider;
import com.finebi.dashboard.impl.service.export.ExportType;
import com.fr.stable.fun.mark.API;

import java.io.OutputStream;

/**
 * Computes the size of the exported Excel file.
 * Created by Hiram on 2018/11/15.
 */
@API(level = ExportHandleProvider.CURRENT_LEVEL)
public abstract class AbstractExportHandleProvider implements ExportHandleProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    @Deprecated
    public OutputStream handleStream(OutputStream originalOutputStream, ExportType type) {
        return originalOutputStream;
    }

}
```

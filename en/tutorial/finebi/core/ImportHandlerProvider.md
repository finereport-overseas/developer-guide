# ImportHandlerProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402891) |

---

## Purpose

Use this interface to inject custom operations before an import action, such as applying custom decryption to the imported file. This interface can be used in conjunction with [ExportHandleProvider](https://kms.fineres.com/pages/viewpage.action?pageId=45369044).

## Interface Content

### Main Interface

```java
package com.finebi.common.excel.api.handler;

import com.fr.stable.fun.mark.Mutable;

import java.io.InputStream;

/**
 * Pre-processes the import stream
 */
public interface ImportHandlerProvider extends Mutable {

    String XML_TAG = "ImportHandlerProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Pre-processes the import stream
     *
     * @param inputStream input stream
     * @param context     processing parameters
     * @return processed stream
     */
    InputStream handleStream(InputStream inputStream, ImportContext context);

}
```

## Registration

```xml
<extra-core>
    <ImportHandlerProvider class="fully qualified class name of your plugin implementation"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
package com.finebi.provider.api.imports;

import com.finebi.common.excel.api.handler.ImportContext;
import com.finebi.common.excel.api.handler.ImportHandlerProvider;
import com.fr.stable.fun.mark.API;

import java.io.InputStream;

/**
 * Pre-processes the import stream.
 * Created by Hiram on 2018/11/15.
 */
@API(level = ImportHandlerProvider.CURRENT_LEVEL)
public abstract class AbstractImportHandlerProvider implements ImportHandlerProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    public InputStream handleStream(InputStream inputStream, ImportContext context) {
        return inputStream;
    }
}
```

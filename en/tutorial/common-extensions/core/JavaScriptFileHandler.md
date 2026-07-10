# JavaScriptFileHandler

| Property | Value |
| --- | --- |
| Interface Type | extra-core / extra-form / extra-report |
| Full Class Name | `com.fr.stable.fun.JavaScriptFileHandler` |

## Interface Purpose

Inject additional JavaScript files.

> **Note:** This interface is only for backward compatibility with older product versions (FR below 10.0). It only takes effect for report preview (cpt/frm templates, excluding mobile and H5 preview) and has no effect in the decision-making platform. Do not use this interface in new plugins.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

public interface JavaScriptFileHandler extends WebFileHandler {

    String XML_TAG = "JavaScriptFileHandler";

    int CURRENT_LEVEL = 1;
}
```

### Related Interfaces

`WebFileHandler` (parent interface):

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface WebFileHandler extends Mutable {

    String ENCODE_SEPARATOR = "?encode=";

    /**
     * Returns the relative paths of the files needed on the web side
     *
     * @return a collection of file paths
     */
    String[] pathsForFiles();

    String encode();
}
```

- `pathsForFiles()`: Returns the relative path of the JS file within the plugin jar, e.g., `com/demo/js/main.js`
- `encode()`: Has a default implementation; returns the resource encoding, defaulting to `UTF-8`

### Plugin Registration

In version 10.0, all three registration methods below only take effect for report preview. Files registered under the `core` tag are loaded first, followed by `form`, then `report`.

```xml
<extra-core>
    <JavaScriptFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-core>
```

```xml
<extra-form>
    <JavaScriptFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-form>
```

```xml
<extra-report>
    <JavaScriptFileHandler class="com.fr.plugin.xxx.youclassname"/>
</extra-report>
```

## Supported Versions

| Product | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported | |
| FR | 9.0 | Supported | |
| FR | 10.0 | Supported | **⚠️ For compatibility only — do not use in new plugins!** |
| FR | 11.0 | Supported | |
| BI | 3.6 | Supported | |
| BI | 4.0 | Supported | |
| BI | 5.1 | Supported | **⚠️ For compatibility only — do not use in new plugins!** |
| BI | 5.1.2 | Supported | **⚠️ For compatibility only — do not use in new plugins!** |
| BI | 5.1.3 | Supported | **⚠️ For compatibility only — do not use in new plugins!** |

## How It Works

**Registration:** When the server/report/decision-report modules (`ServerModule`, `EngineModule`, `FormModule`) start (`module.start()`), they read the declared JS resources from plugins by category.

**Activation:** When `com.fr.web.ResourceHelper#initDefaultJs` and `forceInitJSCache` run, they read the JS file content referenced by the plugin interface and inject it into the main JS for report preview (unlike the Web component interface, which loads JS as a separate file).

## Interface Example

- Demo: [demo-java-script-file-handler](https://code.fanruan.com/hugh/demo-java-script-file-handler)
- Related interface: `CssFileHandler` (comparison of three common plugin interfaces for injecting JS and CSS)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)
- [open-JSD-8210](https://code.fanruan.com/hugh/open-JSD-8210)

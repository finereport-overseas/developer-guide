# CssFileHandler

| Property | Value |
| --- | --- |
| Interface Type | extra-core / extra-form / extra-report |
| Full Class Name | `com.fr.stable.fun.CssFileHandler` |

## Interface Purpose

Inject additional CSS files.

> **Note:** This interface is only for backward compatibility with older product versions (FR below 10.0). It only takes effect for report preview (cpt/frm templates, excluding mobile and H5 preview) and has no effect in the decision-making platform. Do not use this interface in new plugins.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

public interface CssFileHandler extends WebFileHandler {

    String XML_TAG = "CssFileHandler";

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

- `pathsForFiles()`: Returns the relative path of the CSS file within the plugin jar, e.g., `com/demo/css/main.css`
- `encode()`: Has a default implementation; returns the resource encoding, defaulting to `UTF-8`

### Plugin Registration

In version 10.0, all three registration methods below only take effect for report preview. Priority order: `form > report > core`.

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

**Registration:** When the server/report/decision-report modules (`ServerModule`, `EngineModule`, `FormModule`) start (`module.start()`), they read the declared CSS resources from plugins by category.

**Activation:** When `com.fr.web.ResourceHelper#forceInitStyleCache` runs, it reads the CSS file content referenced by the plugin interface and injects it into the main CSS for report preview (unlike the Web component interface, which loads CSS as a separate file).

## Interface Example

- Demo: [demo-css-file-handler](https://code.fanruan.com/hugh/demo-css-file-handler)
- Related interface: `JavaScriptFileHandler` (comparison of three common plugin interfaces for injecting JS and CSS)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)

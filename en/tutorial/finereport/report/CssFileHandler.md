# CssFileHandler

| Property | Value |
| --- | --- |
| Interface Type | extra-core / extra-form / extra-report |
| Full Class Name | `com.fr.stable.fun.CssFileHandler` |

## Purpose

Introduces additional CSS files.

> **Note:** This interface is only for compatibility with older product versions (FR below 10.0). It only takes effect for report previews (cpt/frm templates, excluding mobile and H5 preview) and has no effect in the Decision Platform. Do not use it in new plugins.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

public interface CssFileHandler extends WebFileHandler {

    String XML_TAG = "CssFileHandler";

    int CURRENT_LEVEL = 1;
}
```

### Related Interface

`WebFileHandler` (parent interface):

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface WebFileHandler extends Mutable {

    String ENCODE_SEPARATOR = "?encode=";

    /**
     * Returns the relative paths of the files needed on the web side
     *
     * @return Collection of file paths
     */
    String[] pathsForFiles();

    String encode();
}
```

- `pathsForFiles()`: Returns the relative path of the CSS file inside the plugin JAR, e.g. `com/demo/css/main.css`
- `encode()`: Has a default implementation; returns the resource encoding method, default value is `UTF-8`

### Registration

In version 10.0, all three registration methods below only take effect for report previews. Priority order: `form > report > core`.

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

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes | |
| FR | 9.0 | Yes | |
| FR | 10.0 | Yes | **⚠️ For compatibility only — do not use in new plugins!** |
| FR | 11.0 | Yes | |
| BI | 3.6 | Yes | |
| BI | 4.0 | Yes | |
| BI | 5.1 | Yes | **⚠️ For compatibility only — do not use in new plugins!** |
| BI | 5.1.2 | Yes | **⚠️ For compatibility only — do not use in new plugins!** |
| BI | 5.1.3 | Yes | **⚠️ For compatibility only — do not use in new plugins!** |

## How It Works

**Registration:** When the product's server/report/decision-report modules (`ServerModule`, `EngineModule`, `FormModule`) start (`module.start()`), they read and categorize the CSS resources declared in plugins.

**Effect:** When `com.fr.web.ResourceHelper#forceInitStyleCache` runs, it reads the CSS file content pointed to by the plugin interface and injects it into the main CSS for report previews (unlike web component interfaces, which load CSS as separate independent files).

## Example

- Demo: [demo-css-file-handler](https://code.fanruan.com/hugh/demo-css-file-handler)
- Related interface: `JavaScriptFileHandler` (comparison of three common plugin interfaces for injecting JS and CSS)

## Open-Source Examples

> Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use is prohibited; any consequences of commercial use are solely the responsibility of the user.

- [open-JSD-8035](https://code.fanruan.com/hugh/open-JSD-8035)

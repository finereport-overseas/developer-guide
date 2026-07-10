# SiteTransformer

| Property | Value |
| --- | --- |
| Interface Type | processor |
| Module | extra-core |
| Full Class Name | `com.fr.stable.fun.SiteTransformer` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.stable.fun.SiteTransformer) |

## Overview

Used to replace built-in URL links displayed in the FineReport interface. Typically used in OEM customization scenarios.

## Interface Definition

```java
/**
 * URL transformer for replacing built-in URLs, typically used in OEM scenarios
 */
public interface SiteTransformer extends Mutable {

    String MARK_STRING = "SiteTransformer";

    int CURRENT_LEVEL = 1;

    /**
     * The key to match
     * @param key the key
     * @return true if this is a URL that needs to be replaced, false otherwise
     */
    boolean match(String key);

    /**
     * The new URL after replacement
     * @return the new URL
     */
    String transform();
}
```

## Replaceable Keys

| Key | Description |
| --- | --- |
| `help.register` | Registration URL |
| `help` | Online help documentation URL |
| `register.url` | Registration help documentation URL |
| `register.email` | Registration help email |
| `bbs` | Forum URL |
| `bbs.video` | Video tutorial URL |
| `bbs.questions` | Q&A URL |

## Usage

Register the implementation class via the `extra-core` tag in the plugin's `plugin.xml`:

```xml
<extra-core>
    <SiteTransformer class="com.fr.plugin.xxx.YourSiteTransformer"/>
</extra-core>
```

## Example Code

```java
public class YourSiteTransformer implements SiteTransformer {

    @Override
    public boolean match(String key) {
        // Only replace the forum URL
        return "bbs".equals(key);
    }

    @Override
    public String transform() {
        return "https://your-custom-forum.com";
    }
}
```

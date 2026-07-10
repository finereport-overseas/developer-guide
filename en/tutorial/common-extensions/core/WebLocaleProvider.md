# WebLocaleProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-core |
| Full Class Name | `com.fr.stable.fun.WebLocaleProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.stable.fun.WebLocaleProvider) |

## Overview

Handles custom Locale string parsing logic. Standard Locale formats are `ko_KR`, `ja_JP`, etc., but some browser language packages are non-standard and return abbreviated forms such as `ko` or `ja`. This interface allows you to define custom mappings from such non-standard strings to the correct `Locale` objects.

## Interface Definition

```java
public interface WebLocaleProvider extends Mutable {

    String XML_TAG = "WebLocaleProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Parses a Locale string into a Locale object
     *
     * @param localeStr the locale string passed by the browser (may be in non-standard format)
     * @return the corresponding Locale object
     */
    Locale dispatchLocale(String localeStr);
}
```

## Usage

Register the implementation class via the `extra-core` tag in the plugin's `plugin.xml`:

```xml
<extra-core>
    <WebLocaleProvider class="com.fr.plugin.xxx.YourWebLocaleProvider"/>
</extra-core>
```

## Example Code

The following example maps abbreviated language codes from the browser to full Locale objects:

```java
public class MyWebLocaleProvider implements WebLocaleProvider {

    @Override
    public Locale dispatchLocale(String localeStr) {
        if (localeStr == null) {
            return null;
        }
        switch (localeStr.toLowerCase()) {
            case "ko":
                return Locale.KOREA;
            case "ja":
                return Locale.JAPAN;
            case "zh":
                return Locale.SIMPLIFIED_CHINESE;
            default:
                return null;
        }
    }
}
```

## Notes

- When `dispatchLocale()` returns `null`, the platform will continue with its built-in default parsing logic.
- Only handle special formats relevant to your target scenario; standard formats (e.g., `zh_CN`, `en_US`) do not need to be handled here.
- When multiple implementations are registered simultaneously, the platform calls them in order; the first non-`null` result takes effect.

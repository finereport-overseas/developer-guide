# CustomI18NProvider

| Property | Value |
| --- | --- |
| Module | extra-report (stable) |
| Full Class Name | `com.fr.stable.fun.CustomI18NProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/CustomI18NProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

The `CustomI18NProvider` interface is solely intended to extend the built-in `I18N` template function, enabling dynamic modification or extension of internationalization key-value pairs.

## 3. Interface Overview


```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Selectable;

import java.util.Locale;

/**
 * Created by kerry on 4/8/21
 */
public interface CustomI18NProvider extends Selectable {
    String MARK_STRING = "CustomI18NProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Gets the internationalized value for the given key and locale
     * @param key    Internationalization key
     * @param locale Locale
     * @return Internationalized value
     */
   String getLocText(String key, Locale locale);

}


```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
        <CustomI18NProvider class="your class name"/>
</extra-report>
```

## 6. How It Works


```java
package com.fr.function;

import com.fr.stable.fun.CustomI18NProvider;
// ... other imports ...

public class I18N extends AbstractFunction {

    /**
     * Gets the internationalized value for the given parameter
     *
     * @param args Arguments
     * @return Internationalized value
     */
    public Object run(Object[] args) {
        if (args.length < 1) {
            return Primitive.ERROR_NAME;
        }

        Object var = getCalculatorProvider().resolveVariable(Constants.__LOCALE__);
        if (var == null) {
            try {
                var = getCalculatorProvider().eval("=$" + Constants.__LOCALE__);
            } catch (UtilEvalError e) {
                FineLoggerFactory.getLogger().error(e.getMessage(), e);
            }
        }

        Locale locale = GeneralContext.getLocale();
        if (var instanceof Locale) {
            locale = (Locale) var;
        }

        try {
            String key = GeneralUtils.objectToString(args[0]);
            String locValue = getLocTextFromCustom(key, locale);
            if (!findErrValue(key, locValue)) {
                return locValue;
            }
            // Read from the server first
            locValue = InterProviderFactory.getProvider().getLocText(key, locale);
            // If not found, return the key name
            if (findErrValue(key, locValue)) {
                String newKey = I18nCompatibleUtils.getNewKeyFromOldKey(key);
                // Try the server again with the new key
                locValue = InterProviderFactory.getProvider().getLocText(newKey, locale);
                if (findErrValue(newKey, locValue)) {
                    // Then read from the web
                    locValue = InterProviderFactory.getClientProvider().getEntireKV(locale).get(newKey);
                    if (StringUtils.isEmpty(locValue)) {
                        return newKey;
                    }
                }
            }
            return locValue;
        } catch (Exception e) {
            FineLoggerFactory.getLogger().error(e.getMessage(), e);
            return args[0];
        }
    }

    private String getLocTextFromCustom(String key, Locale locale){
        ExtraReportClassManagerProvider extraReportClassManager = PluginModule.getAgent(PluginModule.ExtraReport);
        if (extraReportClassManager != null) {
            Set<CustomI18NProvider> providers = extraReportClassManager.getArray(CustomI18NProvider.MARK_STRING);
            for (CustomI18NProvider provider : providers) {
                if (provider.selector().accept(new ObjectHolder())) {
                    return provider.getLocText(key, locale);
                }
            }
        }
        return key;
    }

    /**
     * For a provider, if not found, the original value is returned.
     *
     * @param key      Internationalization key
     * @param locValue Local value
     * @return true / false
     */
    private boolean findErrValue(String key, String locValue) {
        return StringUtils.equals(key, locValue);
    }

    // ... rest of the class omitted for brevity ...
}


```

## 7. Constraints and Notes

From a design perspective, this interface is currently too narrow in scope. Avoid using it unless absolutely necessary.

Expanding the interface's applicability would require significant additional wrapping to adapt to front-end internationalization functions.

## 8. Useful Links

Demo: [demo-custom-i18n-provider](https://code.fanruan.com/hugh/demo-custom-i18n-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use is prohibited; any consequences of commercial use are solely the responsibility of the user.

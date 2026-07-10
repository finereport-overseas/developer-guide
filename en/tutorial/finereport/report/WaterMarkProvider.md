# WaterMarkProvider

| Property | Value |
| --- | --- |
| Module | extra-report-core |
| Full Class Name | `com.fr.report.core.watermark.WaterMarkProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/WaterMarkProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

In versions 8.0 and 9.0, the background interface was used to implement watermark effects. However, that interface affected too many areas and led to frequent conflicts between developers. In version 10.0, a dedicated watermark feature was introduced, supporting both global and per-template watermark configurations. However, this built-in feature could only use pre-configured watermark rules and could not support watermarks that depend on dynamic conditions or custom logic. In response, a plugin-based watermark interface was officially released in FineReport 10 and 11 (February 2022), allowing the introduction of custom watermark specifications.

## 3. Interface Overview


```java
package com.fr.report.core.watermark;

import com.fr.base.io.AttrMark;
import com.fr.base.iofile.attr.WatermarkAttr;
import com.fr.stable.fun.mark.Mutable;

/**
 * A watermark loader
 *
 * @author Hoky
 * @date 2021/12/20
 */

public interface WaterMarkProvider extends Mutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "WaterMarkProvider";

    /**
     * Determines whether the watermark provided by this loader is valid
     *
     * @return Whether the watermark loaded by this loader is valid
     */
    boolean isWaterMarkValid(AttrMark template);

    WatermarkAttr getWaterMark(AttrMark template);

    /**
     * The priority level of this watermark loader
     *
     * @return The load order of the watermark
     */
    WaterMarkProvideLevel getWaterMarkProvideLevel();
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
	<WaterMarkProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

Note: This interface is generally not used as a standalone interface. It is typically combined with other configuration or service interfaces.

The first important method is `getWaterMarkProvideLevel()`, which returns the priority level of the interface as an enum:


```java
package com.fr.report.core.watermark;

/**
 * Watermark load priority
 *
 * @author Hoky
 * @date 2021/12/20
 */
public enum WaterMarkProvideLevel {
    PLUGIN(1),
    TEMPLATE(2),
    SERVER(3),
    OTHER(4);

    private Integer level;

    WaterMarkProvideLevel(int level) {
        this.level = level;
    }

    public Integer getLevel() {
        return level;
    }
}
```

Priority (highest to lowest): Plugin watermark > Template watermark > Server global watermark > Other watermark. "Other" is a reserved level with no practical meaning.

Among providers of the same priority, they are evaluated in order. Ultimately, only one watermark provider takes effect. The `isWaterMarkValid` method determines eligibility, as shown in the `WaterMarkFactory` logic:


```java
package com.fr.report.core.watermark;

// ... imports omitted ...

/**
 * Watermark factory
 *
 * @author Hoky
 * @date 2021/12/20
 */
public class WaterMarkFactory {
    private static final Set<WaterMarkProvider> SORTED_LOADING_SET = new TreeSet<>(
            Comparator.comparing(o -> o.getWaterMarkProvideLevel().getLevel())
    );

    public static final WaterMarkProvider DEFAULT_MAKER = new DefaultWaterMarkProvider();

    private WaterMarkFactory() {
        SORTED_LOADING_SET.add(DEFAULT_MAKER);
        SORTED_LOADING_SET.add(new ServerWaterMarkProvider());
        SORTED_LOADING_SET.add(new TemplateWaterMarkProvider());
    }

    /**
     * Loads watermarks in priority order
     *
     * @param template Template
     * @return Watermark attribute
     */
    public WatermarkAttr getWaterMark(AttrMark template) {
        for (WaterMarkProvider waterMarkProvider : SORTED_LOADING_SET) {
            synchronized (this) {
                if (waterMarkProvider.isWaterMarkValid(template)) {
                    return waterMarkProvider.getWaterMark(template);
                }
            }
        }
        return new WatermarkAttr();
    }

    // ... other methods omitted for brevity ...
}


```

The built-in template watermark and server watermark validity checks are implemented in `TemplateWaterMarkProvider` and `ServerWaterMarkProvider` respectively. Developers are encouraged to extend one of these two adapters and customize only the parts they need.

## 7. Constraints and Notes

It is generally recommended to extend one of the two built-in adapters (`TemplateWaterMarkProvider` or `ServerWaterMarkProvider`) and customize only the desired behavior. Rational use of watermark priority levels makes it easier to achieve your goals without necessarily setting the priority to `PLUGIN`.

## 8. Useful Links

Demo: [demo-water-mark-provider](https://code.fanruan.com/hugh/demo-water-mark-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

# DateFormatProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402887) |

---

## Purpose

Provides a plugin-based mechanism for customizing the display format of date-type data in BI, supporting date formats beyond the built-in options (Chinese, "/", "-").

## Interface Content

### Main Interface

```java
@Open
public interface DateFormatProvider extends Immutable {

    String XML_TAG = "DateFormatProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Customizes date formatting for dashboards
     *
     * @param group date grouping
     * @param value raw data value
     * @return formatted date
     */
    Object formatDashboardDate(int group, Object value);

    /**
     * Customizes date formatting for datasets
     *
     * @param group date grouping
     * @param value raw data value
     * @return formatted date
     */
    Object formatTableDate(int group, Object value);
}
```

Description of the `group` type values used in the interface:

### Registration

|   |
| --- |

## Abstract Class Provided to Plugins

```java
@API(level = DateFormatProvider.CURRENT_LEVEL)
public abstract class AbstractDateFormatProvider implements DateFormatProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }

}
```

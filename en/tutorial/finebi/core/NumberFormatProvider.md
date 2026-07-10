# NumberFormatProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402912) |

---

## Purpose

Number formatting in the backend uses patterns such as `##,###.00`, which only support toggling the thousands separator and do not support other formats. This interface provides custom number formatting methods that can be applied to dashboards, data preparation, and Excel exports.

## Interface Content

### Main Interface

```java
@Open
public interface NumberFormatProvider extends Immutable {

    String XML_TAG = "NumberFormatProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Returns the dashboard number formatter
     *
     * @param type            format type: -1 = no processing, 0 = round to integer, 1 = one decimal place, 2 = two decimal places
     * @param isNumSeparators whether to use a thousands separator
     * @return formatter
     */
    DecimalFormat getDashboardDecimalFormat(int type, boolean isNumSeparators);

    /**
     * Returns the Excel number format pattern
     *
     * @param type            format type: -1 = no processing, 0 = round to integer, 1 = one decimal place, 2 = two decimal places
     * @param isNumSeparators whether to use a thousands separator
     * @return format pattern
     */
    String getExcelDecimalFormat(int type, boolean isNumSeparators);

    /**
     * Returns the data table number formatter
     *
     * @param type            format type: -1 = no processing, 0 = round to integer, 1 = one decimal place, 2 = two decimal places
     * @param isNumSeparators whether to use a thousands separator
     * @return formatter
     */
    DecimalFormat getTableDecimalFormat(int type, boolean isNumSeparators);
}
```

### Registration

|   |
| --- |

## Abstract Class Provided to Plugins

```java
@API(level = NumberFormatProvider.CURRENT_LEVEL)
public abstract class AbstractNumberFormatProvider implements NumberFormatProvider {

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

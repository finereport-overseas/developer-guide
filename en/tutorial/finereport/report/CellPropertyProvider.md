# CellPropertyProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.CellPropertyProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/CellPropertyProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

This interface is used to add extra attributes to cells. Its primary purpose is to output JSON configuration for mobile clients. It can also be used to store cell configuration values that are referenced during calculation in conjunction with `HtmlWriter` or the `Actor` interface.

Note: Although it can be used alongside `HtmlWriter` or `Actor` for configuration during calculation, the current design of this interface is not very developer-friendly. Compared to simply adding a configuration item, it carries a relatively high implementation overhead. Unless there is no other viable approach, developers should avoid using this interface. A replacement with the same functionality but a simpler API will be released in a future version.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.json.JSONException;
import com.fr.json.JSONObject;
import com.fr.report.cell.CellElement;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.Repository;
import com.fr.stable.xml.XMLable;

/**
 * Cell property interface
 * @author zack
 * @version 10.0
 * Created by zack on 2020/7/14
 */
public interface CellPropertyProvider extends Mutable, XMLable {
    String MARK_STRING = "CellPropertyProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Unique identifier for the property (XML node tag name)
     * @return
     */
    String xmlTag();

    /**
     * Outputs the property as JSON
     *
     * @param cellJson    Cell JSON object
     * @param cellElement Current cell element
     * @throws JSONException
     * @repository repository Context
     */
    void mixinCellJson(JSONObject cellJson, Repository repository, CellElement cellElement) throws JSONException;

    /**
     * clone
     * @return
     */
    CellPropertyProvider clone();
}
```

This interface is typically used together with `CellPropertyPaneProvider`:


```java
package com.fr.design.fun;

import com.fr.design.cell.CellElementPropertyComponent;

/**
 * Extension interface for cell property pane in the designer
 * @author zack
 * @version 10.0
 * Created by zack on 2020/7/14
 */
public interface CellPropertyPaneProvider extends PropertyItemPaneProvider {

    /**
     * Builds the cell property panel; the panel implementation must use the singleton pattern
     * @return Panel class
     */
    CellElementPropertyComponent getSingletonCelPropertyPane();
}

```


```java
package com.fr.design.fun;

import com.fr.design.mainframe.PaneHolder;
import com.fr.design.mainframe.PropertyItemBean;
import com.fr.stable.fun.mark.Mutable;
import org.jetbrains.annotations.Nullable;

/**
 * created by Harrison on 2020/03/23
 **/
public interface PropertyItemPaneProvider<T> extends Mutable {
    
    int CURRENT_LEVEL = 1;
    
    String XML_TAG = "PropertyItemPaneProvider";
    
    int FIRST = 100;
    
    int LAST = -100;
    
    /**
     * Unique key
     *
     * @return Key
     */
    String key();
    
    /**
     * Property item configuration
     *
     * @return Property item bean
     */
    PropertyItemBean getItem();
    
    /**
     * Pane holder
     *
     * @param clazz Type
     * @return Holder
     */
    @Nullable
    PaneHolder<T> getPaneHolder(Class<?> clazz);
    
    /**
     * The key of the item to replace
     *
     * @return Replace key
     */
    String replaceKey();
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
	<CellPropertyProvider class="your class name"/>
</extra-report>
<extra-designer>
	<PropertyItemPaneProvider class="your class name"/>
</extra-designer>
```

## 6. How It Works

Where needed, all plugin-declared cell extension properties are retrieved via:
`Set<CellPropertyProvider> extraProperties = PluginModule.getAgent(PluginModule.ExtraReport).getArray(CellPropertyProvider.MARK_STRING);`

In the product, this works as follows: all plugin cell attribute types are managed uniformly through `CellPropertyManager`. The report calculation logic reads all plugin-declared cell extension attributes via `CellPropertyManager.getAllCellPropertiesAsMap()` and matches them to the corresponding panel by key for display. Once configured, they are saved through the plugin's own internal event handling (which is also why this interface has a relatively complex usage pattern).

## 7. Constraints and Notes

The property class implemented by a plugin must not contain non-serializable attribute objects, such as `BufferedImage`. If such objects are included, copying and pasting cells will throw a serialization error. Developers must find alternative ways to represent the data, such as storing an image name or a Base64-encoded string.

## 8. Useful Links

Demo: [demo-cell-property-provider](https://code.fanruan.com/hugh/demo-cell-property-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

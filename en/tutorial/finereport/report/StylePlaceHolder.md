# StylePlaceHolder

| Property | Value |
| --- | --- |
| Module | extra-report (stable) |
| Full Class Name | `com.fr.stable.fun.StylePlaceHolder` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/StylePlaceHolder) |

---

## 1. Terminology

None

## 2. Background and Use Cases

As a counterpart to the internal CSS extension approach (which injects plugin-bundled CSS resources), `StylePlaceHolder` is primarily used to introduce third-party CSS resources. Common use cases include integrating third-party charting libraries or importing third-party UI libraries. This interface only applies to cpt reports and Decision Reports.

## 3. Interface Overview


```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;
import com.fr.stable.fun.mark.Mutable;

/**
 * Created by richie on 16/4/26.
 * Style sheet placeholder for injecting stylesheets into page.html or form.html
 */
public interface StylePlaceHolder extends Mutable {

    String MARK_STRING = "StylePlaceHolder";

    int CURRENT_LEVEL = 1;

    /**
     * Placeholder content
     * @return Content
     */
    String placeHolderContent();

    /**
     * Link tag content
     * @return Link tag descriptor objects
     */
    LinkTag[] holderLinks();

    class LinkTag {

        private String type;
        private String href;
        private String rel;

        public static LinkTag build() {
            return new LinkTag();
        }

        private LinkTag() {

        }

        public LinkTag type(String type) {
            this.type = type;
            return this;
        }

        public LinkTag href(String href) {
            this.href = href;
            return this;
        }

        public LinkTag rel(String rel) {
            this.rel = rel;
            return this;
        }

        public String toTag() {
            StringBuilder sb = new StringBuilder();
            sb.append("<link");
            if (StringUtils.isNotEmpty(rel)) {
                sb.append(" rel=");
                sb.append("\"");
                sb.append(rel);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(type)) {
                sb.append(" type=");
                sb.append("\"");
                sb.append(type);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(href)) {
                sb.append(" href=");
                sb.append("\"");
                sb.append(href);
                sb.append("\"");
            }

            sb.append("/>");
            return sb.toString();
        }
    }
}


```


```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.StylePlaceHolder;
import com.fr.stable.fun.mark.API;

/**
 * Created by richie on 16/4/26.
 */
@API(level = StylePlaceHolder.CURRENT_LEVEL)
public abstract class AbstractStylePlaceHolder extends AbstractProvider implements StylePlaceHolder {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }


    public String placeHolderContent() {
        StringBuilder sb = new StringBuilder();
        LinkTag[] linkTags = holderLinks();
        if (linkTags != null) {
            for (LinkTag link : linkTags) {
                sb.append(link.toTag());
            }
        }
        return sb.toString();
    }
}


```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |
| BI | 3.6 | Yes |  |
| BI | 4.0 | Yes |  |
| BI | 5.1 | Yes |  |
| BI | 5.1.2 | Yes |  |
| BI | 5.1.3 | Yes |  |

## 5. Plugin Registration

To inject external CSS for cpt report preview:


```xml
<extra-report>
        <StylePlaceHolder class="your class name"/>
</extra-report>
```

To inject external CSS for Decision Report preview:


```xml
<extra-form>
        <StylePlaceHolder class="your class name"/>
</extra-form>
```

## 6. How It Works

cpt reports: When `ReportletDealWith#dealWithHtml` generates the HTML page content, it directly reads the registered interface implementations from the plugin engine and applies them via `ReportletDealWith#createStylePlaceHolderString`.

Decision Reports: When `AbstractFormActor#initMap4Form` initializes the HTML content, it directly reads the registered interface implementations from the plugin engine and applies them via `AbstractFormActor#createStylePlaceHolderString`.

## 7. Constraints and Notes

This interface is loaded after `com.fr.stable.fun.CssFileHandler` but before `com.fr.decision.fun.WebResourceProvider`.

## 8. Useful Links

Comparison of three common plugin interfaces for injecting JS and CSS

Demo: [demo-style-place](https://code.fanruan.com/hugh/demo-style-place)

`com.fr.stable.fun.JavaScriptPlaceHolder`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

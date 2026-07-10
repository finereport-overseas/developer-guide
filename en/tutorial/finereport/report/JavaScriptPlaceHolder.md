# JavaScriptPlaceHolder

| Property | Value |
| --- | --- |
| Module | extra-report (stable) |
| Full Class Name | `com.fr.stable.fun.JavaScriptPlaceHolder` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/JavaScriptPlaceHolder) |

---

## 1. Terminology

None

## 2. Background and Use Cases

As a counterpart to the internal JS extension approach (which injects plugin-bundled JS resources), `JavaScriptPlaceHolder` is primarily used to introduce third-party JS resources. Common use cases include integrating third-party charting libraries or importing third-party UI libraries.

## 3. Interface Overview


```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;
import com.fr.stable.fun.mark.Mutable;

/**
 * Created by richie on 16/4/26.
 * Script placeholder for injecting scripts into page.html or form.html
 */
public interface JavaScriptPlaceHolder extends Mutable {

    String MARK_STRING = "JavaScriptPlaceHolder";

    int CURRENT_LEVEL = 2;

    /**
     * Placeholder content
     * @return Content
     */
    String placeHolderContent();

    /**
     * Script content
     * @return Script descriptor objects
     */
    ScriptTag[] holderScripts();

    class ScriptTag {

        private String type;
        private String src;
        private String text;

        public static ScriptTag build() {
            return new ScriptTag();
        }

        private ScriptTag() {

        }

        public ScriptTag type(String type) {
            this.type = type;
            return this;
        }

        public ScriptTag src(String src) {
            this.src = src;
            return this;
        }

        public ScriptTag text(String text) {
            this.text = text;
            return this;
        }

        public String toTag() {
            StringBuilder sb = new StringBuilder();
            sb.append("<script");
            if (StringUtils.isNotEmpty(type)) {
                sb.append(" ");
                sb.append("type=");
                sb.append("\"");
                sb.append(type);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(src)) {
                sb.append(" ");
                sb.append("src=");
                sb.append("\"");
                sb.append(src);
                sb.append("\"");
            }
            sb.append(">");
            if (StringUtils.isNotEmpty(text)) {
                sb.append(text);
            }
            sb.append("</script>");
            return sb.toString();
        }
    }

}


```


```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.JavaScriptPlaceHolder;
import com.fr.stable.fun.mark.API;

/**
 * Created by richie on 16/4/26.
 */
@API(level = JavaScriptPlaceHolder.CURRENT_LEVEL)
public abstract class AbstractJavaScriptPlaceHolder extends AbstractProvider implements JavaScriptPlaceHolder {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    public String mark4Provider() {
        return this.getClass().getName();
    }

    public String placeHolderContent() {
        StringBuilder sb = new StringBuilder();
        ScriptTag[] scripts = holderScripts();
        if (scripts != null) {
            for (ScriptTag script : scripts) {
                sb.append(script.toTag());
            }
        }
        return sb.toString();
    }

    public ScriptTag[] holderScripts() {
        return new ScriptTag[0];
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

To inject external JS for cpt report preview:


```xml
<extra-report>
        <JavaScriptPlaceHolder class="your class name"/>
</extra-report>
```

To inject external JS for Decision Report preview:


```xml
<extra-form>
        <JavaScriptPlaceHolder class="your class name"/>
</extra-form>
```

## 6. How It Works

cpt reports: When `ReportletDealWith#dealWithHtml` generates the HTML page content, it directly reads the registered interface implementations from the plugin engine and applies them via `ReportletDealWith#createScriptPlaceHolderString`.

Decision Reports: When `AbstractFormActor#initMap4Form` initializes the HTML content, it directly reads the registered interface implementations from the plugin engine and applies them via `AbstractFormActor#createScriptPlaceHolderString`.

## 7. Constraints and Notes

This interface is loaded after `com.fr.stable.fun.JavaScriptFileHandler` but before `com.fr.decision.fun.WebResourceProvider`.

## 8. Useful Links

Comparison of three common plugin interfaces for injecting JS and CSS

Demo: [demo-java-script-place](https://code.fanruan.com/hugh/demo-java-script-place)

`com.fr.stable.fun.StylePlaceHolder`

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

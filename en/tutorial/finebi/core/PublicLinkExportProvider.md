# PublicLinkExportProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402914) |

---

## Purpose

Provides an interface for enabling export from public links.

## Interface Content

### Main Interface

```java
@Careful
public interface PublicLinkExportProvider extends Immutable {

    String XML_TAG = "PublicLinkExportProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Whether the public link for a given template supports export
     *
     * @param reportId Template ID
     *
     * @return whether export is supported
     */
    boolean isExportable(String reportId);

}
```

## Registration

```xml
<extra-core>
        <PublicLinkExportProvider class="com.finebi.plugin.link.export.DefaultPublicLinkExportProvider"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
@API(level = ServerTablesProvider.CURRENT_LEVEL)
public abstract class AbstractPublicLinkExportProvider implements PublicLinkExportProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }

}
```

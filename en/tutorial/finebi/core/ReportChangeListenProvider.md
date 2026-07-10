# ReportChangeListenProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402916) |

---

## Purpose

Listens for template add, edit, and delete events.

## Interface Content

### Main Interface

```java
public interface ReportChangeListenProvider extends Mutable {


    String XML_TAG = "ReportChangeListenProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Triggered after a template is added
     *
     * @param reportIds Template IDs
     */
    void addEvent(Set<String> reportIds);

    /**
     * Triggered after a template configuration is updated
     *
     * @param reportIds Template IDs
     */
    void updateEvent(Set<String> reportIds);

    /**
     * Triggered after a template configuration is deleted
     *
     * @param reportIds Template IDs
     */
    void removeEvent(Set<String> reportIds);

}
```

## Registration

```xml
<extra-core>
        <ReportChangeListenProvider class="path to your subclass extending the abstract class"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
@API(level = ReportChangeListenProvider.CURRENT_LEVEL)
public abstract class AbstractReportChangeListenProvider implements ReportChangeListenProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

}
```

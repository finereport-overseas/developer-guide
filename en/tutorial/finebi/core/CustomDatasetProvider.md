# CustomDatasetProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402883) |

---

## Purpose

Supports ingesting dataset types beyond the built-in Excel, SQL, and DB types into BI public data.

## Interface Content

```java
@API(level = CustomDatasetProvider.CURRENT_LEVEL)
public abstract class AbstractCustomDatasetProvider implements CustomDatasetProvider {

    @Override
    public int currentAPILevel() {
        return CustomDatasetProvider.CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```

## Registration

```java
    <extra-core>
        <CustomDatasetProvider class="path to your subclass extending AbstractCustomDatasetProvider"/>
    </extra-core>
```

## Abstract Classes Provided to Plugins

Core interface abstract class:

```java
public abstract class AbstractCustomDatasetGenerator implements CustomDatasetGenerator, Serializable {

    @Override
    public boolean match(String name) {
        return CompareUtils.isEqual(name, getName());
    }
}
```

Data retrieval abstract class:

- Without SQL-based retrieval
- With SQL-based retrieval

Frontend page:

```java
@Open
public interface CustomDatasetPageGenerator {

    /**
     * Returns the icon
     */
    String getIcon(CustomDatasetContext datasetContext);

    /**
     * Returns the HTML
     */
    String getEditPageHTML(CustomDatasetContext datasetContext);

    /**
     * Returns the AssembleComponent
     */
    AssembleComponent getComponent(CustomDatasetContext datasetContext);

}
```

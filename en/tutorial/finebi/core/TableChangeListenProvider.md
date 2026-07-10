# TableChangeListenProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402920) |

---

## Purpose

Listens for table add, edit, and delete events.

## Interface Content

### Main Interface

```java
public interface TableChangeListenProvider extends Mutable {

    String XML_TAG = "TableChangeListenProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Triggered after a table is added
     * @param tableNames original table names
     */
    void addEvent(Set<String> tableNames);

    /**
     * Triggered after a table configuration is updated
     * @param tableNames original table names
     */
    void updateEvent(Set<String> tableNames);

    /**
     * Triggered after a table configuration is deleted
     *
     * @param tableNames original table names
     */
    void removeEvent(Set<String> tableNames);

}
```

## Registration

```xml
<extra-core>
        <TableChangeListenProvider class="path to your subclass extending the abstract class"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
@API(level = TableChangeListenProvider.CURRENT_LEVEL)
public abstract class AbstractTableChangeListenProvider implements TableChangeListenProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

}
```

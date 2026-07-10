# UpdateLoadProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402924) |

---

## Purpose

An extension point for the data update lifecycle. All methods return a `boolean`:

- `true`: Skip the corresponding default operation. For example, if `onLoad` returns `true`, the default load operation is not executed.
- `false`: Continue with the corresponding default operation.

## Interface Content

### Main Interface

```java
/**
 * Extension point for the data update lifecycle.
 * All methods return a boolean:
 * true: Skip the corresponding operation. For example, if onLoad returns true, the load operation is skipped.
 * false: Continue with the corresponding operation.
 *
 * @author James.Zhao
 * @version 5.1.3
 * Created by James.Zhao on 2020/4/8
 */
public interface UpdateLoadProvider extends Mutable {

    String XML_TAG = "UpdateLoadProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Called before the update lifecycle starts
     */
    <T extends AbstractLoadProperty> boolean onStart(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * Called before data loading and updating
     */
    <T extends AbstractLoadProperty> boolean onLoad(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * Called before data commit
     */
    <T extends AbstractLoadProperty> boolean onCommit(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * Called before a data table is renamed
     */
    <T extends AbstractLoadProperty> boolean onRename(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * Called after the update lifecycle completes normally
     */
    <T extends AbstractLoadProperty> boolean onEnd(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * Called before a data table rollback
     */
    <T extends AbstractLoadProperty> boolean onRollback(TaskInfoBO bo, AbstractLoad<T> load, Throwable throwable);
}
```

### Registration

```xml
<extra-core>
        <UpdateLoadProvider class="path to your subclass extending the abstract class"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
/**
 * Abstract class for the update lifecycle extension point
 *
 * @author James.Zhao
 * @version 5.1.3
 * Created by James.Zhao on 2020/4/8
 */
@API(level = UpdateLoadProvider.CURRENT_LEVEL)
public abstract class AbstractUpdateLoadProvider implements UpdateLoadProvider {
    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onLoad(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onCommit(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onRename(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onEnd(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onRollback(TaskInfoBO bo, AbstractLoad<T> load, Throwable throwable) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onStart(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }
}
```

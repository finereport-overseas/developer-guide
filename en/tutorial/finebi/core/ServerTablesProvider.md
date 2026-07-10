# ServerTablesProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402918) |

---

## Purpose

Allows controlling the visible scope of server datasets based on user information.

## Interface Content

### Main Interface

```java
@Careful
public interface ServerTablesProvider extends Immutable {

    String XML_TAG = "ServerTableListProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Returns the set of server dataset names visible to the specified user
     *
     * @param userId User ID
     * @return server dataset names (original names)
     */
    List<String> getServerTableNames(String userId);

}
```

## Registration

```xml
<extra-core>
    <ServerTableListProvider class="com.fr.plugin.demo.server.CustomServerTablesProvider"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
@API(level = ServerTablesProvider.CURRENT_LEVEL)
public abstract class AbstractServerTablesProvider implements ServerTablesProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }
}
```

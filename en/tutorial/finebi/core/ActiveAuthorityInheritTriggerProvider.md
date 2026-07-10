# AuthorityInheritTriggerProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-core (FineBI) |
| Full Class Name | `com.finebi.common.api.provider.AuthorityInheritTriggerProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402881) |

## Overview

An interface that controls whether the permission inheritance toggle for self-service dataset source tables is enabled. The toggle is hidden by default (not shown in the theme settings); enabling it requires a plugin implementation.

## Interface Definition

```java
public interface AuthorityInheritTriggerProvider extends Mutable {

    String XML_TAG = "AuthorityInheritTriggerProvider";

    /**
     * Whether to enable the permission inheritance toggle for analysis source tables
     */
    boolean originTableInheritTriggerEnable();
}
```

It is recommended to extend the abstract base class `AbstractAuthorityInheritTriggerProvider`, which already implements `currentAPILevel()` and `mark4Provider()`:

```java
@API(level = Constants.Extension.NOT_STACKABLE_API_LEVEL)
public abstract class AbstractAuthorityInheritTriggerProvider implements AuthorityInheritTriggerProvider {

    @Override
    public int currentAPILevel() {
        return Constants.Extension.NOT_STACKABLE_API_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` via the `extra-core` tag:

```xml
<extra-core>
    <AuthorityInheritTriggerProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-core>
```

The platform reads the toggle value provided by the plugin as follows:

```java
Set<AuthorityInheritTriggerProvider> providers =
    ExtraClassManager.getInstance().getArray(AuthorityInheritTriggerProvider.XML_TAG);
if (CollectionUtils.isNotEmpty(providers)) {
    providers.stream().findAny().ifPresent(i ->
        OriginTableInheritTriggerManager.INSTANCE
            .registerOriginTableInheritTriggerProvider(i.originTableInheritTriggerEnable()));
}
```

## Example Code

```java
@EnableMetrics
public class ActiveAuthorityInheritTriggerProvider extends AbstractAuthorityInheritTriggerProvider {

    @Override
    @Focus(
        id = "com.finebi.plugin.bi.authority.inherit.ActiveAuthorityInheritTriggerProvider",
        text = "BI Source Table Permission Inheritance Toggle",
        source = Original.PLUGIN
    )
    public boolean originTableInheritTriggerEnable() {
        // Return true to enable the source table permission inheritance toggle
        return true;
    }
}
```

## Notes

- This interface is non-stackable (`NOT_STACKABLE_API_LEVEL`). When multiple implementations are registered simultaneously, only one takes effect (via `findAny()`).
- The toggle is hidden by default and will only appear in the theme settings after a plugin implementation is registered.

# Plugin Lifecycle

## Lifecycle Stage Reference

| Stage | Description |
|---|---|
| **load** | Reads and creates the `PluginContext` object; adds inter-plugin dependency classpaths; initializes interface Class objects; reads the license |
| **run** | Checks plugin-required files; creates the `PluginClassLoader`; checks and starts plugin-required services; instantiates plugin interface objects and registers them in each module's `ExtraClassManager` |
| **stop** | Removes objects from `ExtraClassManager`; clears objects in `PluginContext`; destroys `PluginClassLoader`; attempts to stop service dependencies |
| **forbid** | Executes stop; sets the `active` attribute in `plugin.xml` to disabled |
| **enable** | Sets the `active` attribute in `plugin.xml` to enabled; re-reads the license; executes run |
| **unload** | Counterpart of load; executes stop; removes Class objects loaded into `PluginContext`; removes the `PluginContext` object |
| **install** | Checks for required files, services, and inter-plugin dependencies; installs dependencies; copies files to the plugin home |
| **uninstall** | Counterpart of install; checks dependencies; deletes files |
| **update** | Executes stop; installs the new version; moves the license to the new version directory; deletes the old plugin |

---

## Listening to the Plugin Lifecycle

### Listening for Your Own Plugin's start / stop Events

With hot deployment support, every plugin must release any resources it holds after being stopped. This is done via the `lifecycle-monitor` tag:

```xml
<!-- plugin.xml -->
<lifecycle-monitor class="com.fr.plugin.performance.base.manager.monitor.PerformancePluginMonitor"/>
```

The specified class must extend `AbstractPluginLifecycleMonitor`:

```java
public class PerformancePluginMonitor extends AbstractPluginLifecycleMonitor {

    @Override
    public void afterRun(PluginContext context) {
        // Called after the plugin starts — initialize caches, register listeners, etc.
    }

    @Override
    public void beforeStop(PluginContext context) {
        // Called before the plugin stops — release resources
    }
}
```

`AbstractPluginLifecycleMonitor` also provides three additional lifecycle callbacks:

| Method | When it fires |
|---|---|
| `afterInstall(PluginContext)` | After installation completes |
| `beforeUninstall(PluginContext)` | Before uninstallation |
| `afterUpdate(PluginContext)` | After an update completes |

> Note: These three events occur outside the plugin's running lifecycle. The `ExtraClassManager` does not contain any objects from the current plugin at these points — do not rely on runtime-related resources.

---

## Fine-Grained Lifecycle Listening (FineReport side)

When FineReport needs to cache plugin objects, the plugin event model can be used to listen for lifecycle changes on specific plugins.

### 1. Define a Listener

```java
PluginEventListener listener = new PluginEventListener(
        PluginListenerPriority.StableFactory,
        PluginListenerScope.ServletContextScope) {

    @Override
    public void on(PluginEvent event) {
        refreshJavaScriptFiles();
    }
};
```

- `priority`: determines execution order when multiple listeners are registered — higher priority fires first.
- `scope`: used for automatic listener removal (e.g., `ServletContextScope` auto-removes the listener when the Servlet shuts down).

### 2. Define a Filter

```java
PluginFilter filter = new PluginFilter() {

    @Override
    public boolean accept(PluginContext context) {
        // Only listen to plugins that contain a JavaScriptFileHandler
        return context.contain("JavaScriptFileHandler");
    }
};
```

### 3. Register the Listener

```java
// Refresh JS files whenever a matching plugin stops
PluginListenerRegistration.getInstance().listen(
        PluginEventType.AfterStop,
        listener,
        filter);
```

The supported event types are defined in the `PluginEventType` enum, covering the major lifecycle stages described in the table at the top of this page.

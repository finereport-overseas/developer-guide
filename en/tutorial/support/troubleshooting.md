# Troubleshooting

## Plugin JAR Conflicts with the Host Application

### Problem

During plugin development, you may include third-party libraries that conflict with libraries already present on the server, causing the plugin to fail at runtime.

### Solution

In versions released after 2019-07-03, `plugin.xml` supports the `prefer-packages` attribute to resolve this issue.

In versions released after 2020-03-25, `prefer-packages` also supports the wildcard `*` for bulk configuration.

### Examples

Load a single package with priority:

```xml
<prefer-packages>
    <prefer-package>com.fr.harrison</prefer-package>
</prefer-packages>
```

With this configuration, when a class such as `com.fr.harrison.test1` is loaded, the standard parent-delegation model is bypassed and the class is loaded from `plugin/classes` or `plugin/lib` first, avoiding conflicts with server libraries.

Multiple packages can be specified with multiple child nodes:

```xml
<prefer-packages>
    <prefer-package>com.fr.harrison</prefer-package>
    <prefer-package>com.fr.alex</prefer-package>
</prefer-packages>
```

Using wildcards:

```xml
<prefer-packages>
    <prefer-package>*</prefer-package>
    <prefer-package>com.fr.*</prefer-package>
    <prefer-package>com.fr.*.harrion.*</prefer-package>
</prefer-packages>
```

---

## Plugin Installed but Not Taking Effect

**Scenario 1**: A custom function plugin is installed and visible in the Plugin Manager, but the function cannot be found in the formula editor's plugin functions list.

Cause: The plugin is missing a feature-point recorder class, or the recorder class reference is incorrect. For example, `<function-recorder>` was misspelled as `<function-recoder>`, or the class referenced by the feature-point annotation (`com.xx.zz.ABC`) is missing the `<function-recorder class="com.xx.zz.ABC"/>` declaration.

**Scenario 2**: The plugin interface's API level is too low, causing the plugin to be disabled.

Fix: Update the implementation to a higher API level.

---

## Common Plugin Errors

### `XXX cannot be cast to com.fr.plugin.basic.Closeable`

The plugin engine modifies classes declared in `plugin.xml` to add the `Closeable` interface for hot-deployment support. This error indicates the interface was not added successfully. Check the following causes one by one:

- By default, the engine only processes classes under the `com.fr.plugin` package (10.0 plugins). If the class declared in `plugin.xml` is outside this package, add a `<main-package>` tag to specify the correct package.
- The class was loaded by a parent ClassLoader — for example, a stale `.class` file or JAR still exists on the report's classpath. Due to parent delegation, it gets loaded from the report first, preventing the plugin engine from processing it.
- If launched from source code, check the plugin's compile output path, and verify that no report module depends on the plugin module (a common 8.0 debugging pattern). Such a dependency causes the report's ClassLoader to load plugin classes first.

### Other "Failed to initialize plugin object" errors

Some interface changes may cause plugin incompatibilities. For example:

- `FRContext.getLogger()` signature change: in 8.0 it returns `FRLogger`; in 10.0 it returns `FRLoggerProvider`. A plugin built against 8.0 will throw a "method not found" error in 10.0. Fix by changing the call to `FRLogger.getLogger()`.

### Designer works correctly, but Tomcat reports "Plugin has no feature-point record and cannot run"

When Tomcat starts, designer-related classes in the plugin are not loaded, so the feature-point recorder cannot be found.

Fix: Move the feature-point recording to a server-side class.

---

## Cannot Download Dependency JARs

If you have configured a custom Maven mirror, it may block downloads from FanRuan's private Maven repository. Refer to the Maven mirror configuration documentation to resolve this.

---

## Cannot Open the Plugin Manager

When opening the Plugin Manager from the designer, the following error appears:

```
11:29:06 SEVERE: Failed to extract Chromium binaries into
C:\Users\Administrator\AppData\Local\JxBrowser\browsercore-69.0.3497.12.6.23.1
com.teamdev.jxbrowser.chromium.internal.ChromiumExtractorException: The /chromium-win32.7z resource cannot be found in JAR files
    at com.teamdev.jxbrowser.chromium.internal.ChromiumExtractor.a(SourceFile:83)
    at com.teamdev.jxbrowser.chromium.internal.ChromiumExtractor.extractChromiumBinariesIfNecessary(SourceFile:58)
..............................
```

**Solution 1**: Update to the latest `report-starter-10` project, then re-import and run `gradle install`.

**Solution 2**: Edit the `build.gradle` file under `report-starter-10`. In the `dependencies` block, find:

```groovy
implementation 'com.fr.third:jxbrowser:6.23.1'
implementation 'com.fr.third:jxbrowser-mac:6.23.1'
```

Add the following dependency below them:

```groovy
implementation 'com.fr.third:jxbrowser-win64:6.23.1'
```

Then run `gradle install` again to copy the newly downloaded `jxbrowser-win64` JAR into the classpath, and restart the designer.

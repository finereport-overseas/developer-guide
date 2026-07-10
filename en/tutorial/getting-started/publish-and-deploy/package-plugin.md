# Package Plugin

Through the previous sections, we can now develop a working plugin. However, we still cannot distribute it to others (for installation or upload to the plugin marketplace). This section covers how to build a distributable plugin package.

---

## Build an Encrypted Plugin Package

Navigate to the plugin source directory. Check the value of `ext.guard` — it defaults to `false`. To build an encrypted package, change this value to `true`.

Run the command:

```text
gradle zip
```

After the build completes, the plugin installation package (a ZIP file) will be available under the plugin source directory at `/build/install`. Plugin code built with this configuration cannot be decompiled.

Key build configuration:

```groovy
ext {
    /**
     * Path to JARs used in the project.
     * 1. If a JAR needs to be included in the ZIP, place it in the lib root directory.
     * 2. If a JAR is only needed at compile time, place it in a subdirectory under lib.
     */
    libPath = "$projectDir/../webroot/WEB-INF/lib"

    /**
     * Whether to encrypt the plugin's class files to prevent decompilation.
     */
    guard = true

    def pluginInfo = getPluginInfo()
    pluginPre = "fine-plugin"
    pluginName = pluginInfo.id
    pluginVersion = pluginInfo.version

    outputPath = "$projectDir/../webroot/WEB-INF/plugins/plugin-" + pluginName + "-1.0/classes"
}
```

> **Note:** Do not enable encryption mode while debugging a plugin.

---

## Build an Unencrypted Plugin Package

Navigate to the plugin source directory. The default value of `ext.guard` is `false` — leave it as is.

Run the command:

```text
gradle zip
```

After the build completes, the plugin installation package (a ZIP file) will be available under the plugin source directory at `/build/install`. Plugin code built with this configuration can be decompiled.

Key build configuration (the `com.fr.plugin.pack.PluginPretreatmentTask` task is omitted):

```groovy
ext {
    /**
     * Path to JARs used in the project.
     * 1. If a JAR needs to be included in the ZIP, place it in the lib root directory.
     * 2. If a JAR is only needed at compile time, place it in a subdirectory under lib.
     */
    libPath = "$projectDir/../webroot/WEB-INF/lib"

    /**
     * Whether to encrypt the plugin's class files to prevent decompilation.
     */
    guard = false

    def pluginInfo = getPluginInfo()
    pluginPre = "fine-plugin"
    pluginName = pluginInfo.id
    pluginVersion = pluginInfo.version

    outputPath = "$projectDir/../webroot/WEB-INF/plugins/plugin-" + pluginName + "-1.0/classes"
}
```

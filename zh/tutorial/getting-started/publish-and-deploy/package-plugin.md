# 打包插件

通过前面几节的学习，我们已经可以开发出来一个可用的插件了，但是还无法把开发的插件包分发给其他人用（给其他人安装或者上传到插件商城），这一节就是展示如何构建一个可分发的插件包。

---

## 构建加密插件包

进入插件源码目录，注意查看 `ext.guard` 的值，默认为 `false`，需要构建加密插件包，要将这个值改为 `true`。

执行命令：

```text
gradle zip
```

执行完毕后，可以在插件源码目录 `/build/install` 目录下看到构建好的插件安装包（一个 zip 文件），使用这种配置构建出来的插件代码，无法反编译。

关键构建配置：

```groovy
ext {
    /**
     * 项目中依赖的jar的路径
     * 1.如果依赖的jar需要打包到zip中,放置在lib根目录下
     * 2.如果依赖的jar仅仅是编译时需要，防止在lib下子目录下即可
     */
    libPath = "$projectDir/../webroot/WEB-INF/lib"

    /**
     * 是否对插件的class进行加密保护，防止反编译
     */
    guard = true

    def pluginInfo = getPluginInfo()
    pluginPre = "fine-plugin"
    pluginName = pluginInfo.id
    pluginVersion = pluginInfo.version

    outputPath = "$projectDir/../webroot/WEB-INF/plugins/plugin-" + pluginName + "-1.0/classes"
}
```

> **注意：** 在进行插件调试时，不可开启加密保护模式。

---

## 构建不加密插件包

进入插件源码目录，注意查看 `ext.guard` 的值，默认为 `false` 即可。

执行命令：

```text
gradle zip
```

执行完毕后，可以在插件源码目录 `/build/install` 目录下看到构建好的插件安装包（一个 zip 文件），使用这种配置构建出来的插件代码，可以被反编译。

关键构建配置（去掉了 `com.fr.plugin.pack.PluginPretreatmentTask` 任务）：

```groovy
ext {
    /**
     * 项目中依赖的jar的路径
     * 1.如果依赖的jar需要打包到zip中,放置在lib根目录下
     * 2.如果依赖的jar仅仅是编译时需要，防止在lib下子目录下即可
     */
    libPath = "$projectDir/../webroot/WEB-INF/lib"

    /**
     * 是否对插件的class进行加密保护，防止反编译
     */
    guard = false

    def pluginInfo = getPluginInfo()
    pluginPre = "fine-plugin"
    pluginName = pluginInfo.id
    pluginVersion = pluginInfo.version

    outputPath = "$projectDir/../webroot/WEB-INF/plugins/plugin-" + pluginName + "-1.0/classes"
}
```

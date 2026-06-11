# 故障排查

## 插件和主工程 jar 冲突

### 问题

插件开发中，有时候我们会在内部使用一些第三方的 lib，这些 lib 可能会与服务器中的 lib 相冲突，从而导致插件无法继续运行。

### 解决方案

在 2019.07.03 后的版本中，`plugin.xml` 加入了 `prefer-packages` 属性来解决此问题。

在 2020.03.25 后的版本中，`prefer-packages` 属性支持通配符 `*`，方便批量设置。

### 案例

单个包优先加载：

```xml
<prefer-packages>
    <prefer-package>com.fr.harrison</prefer-package>
</prefer-packages>
```

配置后，加载到相关类（如 `com.fr.harrison.test1`）时，会避免双亲委派，优先从 `plugin/classes` 或 `plugin/lib` 中读取，从而避免与服务器 lib 冲突。

多个包可使用多个子节点：

```xml
<prefer-packages>
    <prefer-package>com.fr.harrison</prefer-package>
    <prefer-package>com.fr.alex</prefer-package>
</prefer-packages>
```

通配符使用方式：

```xml
<prefer-packages>
    <prefer-package>*</prefer-package>
    <prefer-package>com.fr.*</prefer-package>
    <prefer-package>com.fr.*.harrion.*</prefer-package>
</prefer-packages>
```

---

## 插件已安装但没有生效

**场景一**：开发了自定义函数插件，插件管理器中可以看到插件，但在公式界面的插件函数中找不到该函数。

原因：插件漏写了功能点记录类，或功能点记录类指向错误。例如把 `<function-recorder>` 拼写成了 `<function-recoder>`，或功能点标记指向的类 `com.xx.zz.ABC` 没有加注解标记 `<function-recorder class="com.xx.zz.ABC"/>`。

**场景二**：插件接口的 API 等级过低，导致插件被禁用。

解决：将代码中的实现改成更高的 API Level 即可。

---

## 插件常见报错应对

### `XXX cannot be cast to com.fr.plugin.basic.Closeable`

插件引擎会修改 `plugin.xml` 中描述的类，为其添加 `Closeable` 接口辅助热部署。出现此报错说明接口未能成功添加，可能原因如下，逐一排查：

- 引擎默认只处理 `com.fr.plugin`（10.0 插件）包下的类。如果 `plugin.xml` 中的类不在此包下，需要指定 `<main-package>` 标签。
- 该类被上层类加载器加载了——比如有遗留的 class 或 jar 包仍在报表的 classpath 下，由于双亲委派机制会优先从报表中加载，导致插件引擎无法处理该类。
- 如果是代码启动的，先检查插件的编译路径，再检查是否有报表模块依赖了插件模块（8.0 常见的调试方式），若有依赖也会导致报表的 classLoader 先加载插件类。

### 其他 XXX 初始化插件对象失败

部分接口有变动，可能造成插件不兼容。例如：

- `FRContext.getLogger()` 接口变动：8.0 中返回 `FRLogger`，10.0 中返回 `FRLoggerProvider`。若用 8.0 打包插件，在 10.0 中会报找不到该方法，修改为 `FRLogger.getLogger()` 即可。

### 设计器运行正常，Tomcat 上报"监测到插件没有功能点记录，无法运行"

Tomcat 启动时不会加载插件中与设计器有关的类，导致找不到功能点记录。

解决：将功能点记录放到服务器相关的类上。

---

## 无法下载依赖 jar 包

如果开发人员自己配置了 Maven 镜像，可能导致无法从帆软私有 Maven 仓库下载 jar 包。请参考 Maven 服务器镜像配置相关文档解决此问题。

---

## 无法启动插件管理器

通过设计器打开插件管理器时，出现如下报错：

```
11:29:06 严重: Failed to extract Chromium binaries into
C:\Users\Administrator\AppData\Local\JxBrowser\browsercore-69.0.3497.12.6.23.1
com.teamdev.jxbrowser.chromium.internal.ChromiumExtractorException: The /chromium-win32.7z resource cannot be found in JAR files
    at com.teamdev.jxbrowser.chromium.internal.ChromiumExtractor.a(SourceFile:83)
    at com.teamdev.jxbrowser.chromium.internal.ChromiumExtractor.extractChromiumBinariesIfNecessary(SourceFile:58)
..............................
```

**解决方案一**：更新到最新的 `report-starter-10` 开发工程，然后重新 reimport 并执行 `gradle install`。

**解决方案二**：修改 `report-starter-10` 下的 `build.gradle` 文件，在 `dependencies` 中找到：

```groovy
implementation 'com.fr.third:jxbrowser:6.23.1'
implementation 'com.fr.third:jxbrowser-mac:6.23.1'
```

在其下方增加一个依赖：

```groovy
implementation 'com.fr.third:jxbrowser-win64:6.23.1'
```

然后重新执行 `gradle install` 将新下载的 `jxbrowser-win64` jar 包拷贝到 classpath 中，再次启动即可。

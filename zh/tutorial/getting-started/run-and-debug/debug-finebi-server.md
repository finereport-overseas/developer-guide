# 调试 FineBI 服务器

FineBI 没有独立的设计器进程，插件运行在嵌入式 Tomcat 服务器中，因此调试方式与 FineReport 不同，需要以**远程调试**模式启动服务器，再从 IDEA 附加调试器。

---

## 前置条件

- 已完成 [配置插件开发环境](../environment-setup/plugin-dev-env.md) 中的项目搭建
- 插件模块的输出路径已按格式配置（详见下方）

---

## 第一步：配置插件输出路径

与 FineReport 相同，FineBI 插件的输出目录必须遵循以下格式才能被热加载：

```
${WORK_HOME}/plugins/plugin-{插件ID}-{版本号}/classes
```

例如，插件 ID 为 `com.fr.plugin.bi.demo`，版本为 `1.0`，则输出路径为：

```
${WORK_HOME}/plugins/plugin-com.fr.plugin.bi.demo-1.0/classes
```

在 IntelliJ IDEA 的 **Project Structure → Modules → Paths** 中修改 `Output path`，或在 `pom.xml` 中配置：

```xml
<build>
    <outputDirectory>${WORK_HOME}/plugins/plugin-com.fr.plugin.bi.demo-1.0/classes</outputDirectory>
</build>
```

同时，将 `plugin.xml` 复制到该目录的上一层：

```
${WORK_HOME}/plugins/plugin-com.fr.plugin.bi.demo-1.0/
├── plugin.xml
└── classes/
    └── com/fr/plugin/...
```

---

## 第二步：以远程调试模式启动 FineBI 服务器

在项目根目录找到启动脚本，添加 JVM 远程调试参数后启动：

**Windows（`startup.bat`）：**

```bat
set JAVA_OPTS=-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005
```

**Linux/Mac（`startup.sh`）：**

```bash
export JAVA_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005"
```

参数说明：
- `suspend=n`：服务器不等待调试器连接即可启动（改为 `y` 则在连接前挂起）
- `address=5005`：调试端口，可按需修改

---

## 第三步：在 IDEA 中配置远程调试

1. 打开 **Run → Edit Configurations**
2. 点击 **+** → 选择 **Remote JVM Debug**
3. 填写配置：
   - **Host**：`localhost`
   - **Port**：`5005`（与启动脚本一致）
4. 点击 **OK** 保存

启动服务器后，在 IDEA 中点击 **Debug** 按钮连接，控制台显示 `Connected to the target VM` 即表示附加成功。

---

## 第四步：触发断点

在插件代码中设置断点，通过浏览器访问对应的 FineBI 功能页面触发，IDEA 即会在断点处暂停。

---

## 注意事项

- 修改 Java 代码后需要**重新 Build 模块**，输出目录中的 `.class` 文件才会更新；无需重启服务器（热替换通过 IDEA 的 **HotSwap** 功能支持，但存在限制）
- 若修改了 `plugin.xml`，需要重启服务器才能生效
- 生产环境中请移除调试参数，远程调试端口对外暴露存在安全风险

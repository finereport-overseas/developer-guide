# 日志查看技巧

帆软插件开发中，日志是定位问题最直接的手段。FR/BI 使用帆软自研的 `FineLoggerFactory` 日志框架，底层基于 Log4j。

---

## 在插件中输出日志

```java
import com.fr.log.FineLoggerFactory;
import com.fr.log.FineLogger;

public class MyPlugin {
    private static final FineLogger LOGGER = FineLoggerFactory.getLogger();

    public void doSomething() {
        LOGGER.info("插件执行开始");
        try {
            // ...
        } catch (Exception e) {
            LOGGER.error("插件执行出错：{}", e.getMessage(), e);
        }
    }
}
```

常用日志级别（从详细到简略）：`DEBUG` → `INFO` → `WARN` → `ERROR`

---

## 日志文件位置

FR/BI 的日志文件统一写入工作目录的 `logs/` 子目录：

```
${WORK_HOME}/logs/
├── fanruan.log          ← 当天日志（滚动写入）
├── fanruan.log.1        ← 前一天
├── fanruan.log.2        ← 前两天
└── ...
```

`${WORK_HOME}` 默认为：
- FineReport 设计器：`~/FineReport_11.5/`（或在运行配置中指定的工作目录）
- FineBI 服务器：Tomcat 工作目录下的 `webapps/webroot/WEB-INF/`

---

## 在 IDEA 控制台查看日志

本地调试时，日志会同时输出到 IDEA 的 **Run/Debug** 控制台。可使用控制台顶部的过滤框按关键字筛选，例如输入插件包名 `com.fr.plugin` 快速定位相关日志。

---

## 调整日志级别

日志级别通过 `${WORK_HOME}/resources/log4j2.xml`（或 `log4j.properties`）配置：

```xml
<!-- 将指定包的日志级别调低，输出更多调试信息 -->
<Logger name="com.fr.plugin.mypackage" level="DEBUG" additivity="false">
    <AppenderRef ref="Console"/>
    <AppenderRef ref="RollingFile"/>
</Logger>
```

修改配置后重启服务/设计器即可生效。

---

## 常用排查手法

| 场景 | 方法 |
| --- | --- |
| 插件未加载 | 搜索日志中的插件 ID，查找 `load plugin` 或 `Exception` 关键字 |
| 接口未生效 | 在接口实现类的入口方法打 `INFO` 日志，确认是否被调用 |
| 数据异常 | 在关键分支打印输入参数和中间变量 |
| 性能问题 | 在耗时操作前后各打一行 `INFO`，计算时间差 |

---

## 实时跟踪日志文件

在命令行中实时监控日志输出：

**Windows（PowerShell）：**
```powershell
Get-Content "${WORK_HOME}\logs\fanruan.log" -Wait -Tail 100
```

**Linux/Mac：**
```bash
tail -f ${WORK_HOME}/logs/fanruan.log
```

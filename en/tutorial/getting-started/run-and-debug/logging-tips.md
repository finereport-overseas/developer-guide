# Logging Tips

In FanRuan plugin development, logs are the most direct way to diagnose issues. FR/BI uses FanRuan's in-house `FineLoggerFactory` logging framework, which is built on top of Log4j.

---

## Writing Log Output in a Plugin

```java
import com.fr.log.FineLoggerFactory;
import com.fr.log.FineLogger;

public class MyPlugin {
    private static final FineLogger LOGGER = FineLoggerFactory.getLogger();

    public void doSomething() {
        LOGGER.info("Plugin execution started");
        try {
            // ...
        } catch (Exception e) {
            LOGGER.error("Plugin execution error: {}", e.getMessage(), e);
        }
    }
}
```

Common log levels (most verbose to least): `DEBUG` → `INFO` → `WARN` → `ERROR`

---

## Log File Location

FR/BI writes all log output to the `logs/` subdirectory of the working directory:

```
${WORK_HOME}/logs/
├── fanruan.log          ← today's log (rolling)
├── fanruan.log.1        ← yesterday
├── fanruan.log.2        ← two days ago
└── ...
```

`${WORK_HOME}` defaults to:
- FineReport Designer: `~/FineReport_11.5/` (or the working directory specified in the run configuration)
- FineBI Server: `webapps/webroot/WEB-INF/` under the Tomcat working directory

---

## Viewing Logs in the IDEA Console

During local debugging, log output is also printed to the IDEA **Run/Debug** console. Use the filter box at the top of the console to search by keyword — for example, enter your plugin package name `com.fr.plugin` to quickly find related log entries.

---

## Adjusting the Log Level

Log levels are configured in `${WORK_HOME}/resources/log4j2.xml` (or `log4j.properties`):

```xml
<!-- Lower the log level for a specific package to get more debug output -->
<Logger name="com.fr.plugin.mypackage" level="DEBUG" additivity="false">
    <AppenderRef ref="Console"/>
    <AppenderRef ref="RollingFile"/>
</Logger>
```

Restart the server or designer after modifying the configuration for changes to take effect.

---

## Common Troubleshooting Patterns

| Scenario | Approach |
| --- | --- |
| Plugin not loading | Search the log for the plugin ID; look for `load plugin` or `Exception` keywords |
| Interface not taking effect | Add an `INFO` log at the entry point of the interface implementation to confirm it is being called |
| Data anomalies | Print input parameters and intermediate variables at key branching points |
| Performance issues | Log an `INFO` line before and after the slow operation to measure elapsed time |

---

## Tailing the Log File in Real Time

Monitor log output live from the command line:

**Windows (PowerShell):**
```powershell
Get-Content "${WORK_HOME}\logs\fanruan.log" -Wait -Tail 100
```

**Linux/Mac:**
```bash
tail -f ${WORK_HOME}/logs/fanruan.log
```

# ReportScheduleResultProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.schedule.extension.report.provider.ReportScheduleResultProvider` |

## Background and Use Cases

Scheduled tasks typically archive reports as CPR or FRR format attachments. This interface allows you to generate attachments in alternative formats and have them displayed as snapshot reports through the decision platform.

## Interface Definition

```java
package com.fr.schedule.extension.report.provider;

import com.fr.stable.fun.mark.Mutable;

public interface ReportScheduleResultProvider extends Mutable {
    String MARK_STRING = "ReportScheduleResultProvider";
    int CURRENT_LEVEL = 1;

    ScheduleResultProvider customResult();
}
```

```java
public interface ScheduleResultProvider {

    String getResultSuffix();

    Weblet accessResult(HttpServletRequest req, String path,
                        String fileName, ScheduleShowType showType)
            throws Exception;
}
```

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <ReportScheduleResultProvider class="your class name"/>
</extra-decision>
```

## How It Works

`ScheduleResultBox` maintains a concurrent registry keyed by file suffix. When a result file is accessed, the system iterates through the discovered files and matches them against registered providers by suffix.

## Notes

- This interface only takes effect when no `.cpr`/`.frr` files exist in the attachment list
- The interface's effectiveness depends on the file list order. Behavior is unpredictable when conflicting files exist; use with caution

## Useful Links

- Demo: [demo-report-schedule-result-provider](https://code.fanruan.com/hugh/demo-report-schedule-result-provider)

# ReportScheduleResultProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.schedule.extension.report.provider.ReportScheduleResultProvider` |

## 背景与场景

定时任务通常将报表归档为 CPR 或 FRR 格式附件。该接口允许自定义生成替代格式的附件，并通过决策平台将其展示为快照报表。

## 接口定义

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

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <ReportScheduleResultProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`ScheduleResultBox` 维护以文件后缀为键的并发注册表。访问结果文件时，系统遍历已发现的文件，按后缀匹配已注册的 provider。

## 注意事项

- 仅当附件列表中不存在 `.cpr`/`.frr` 文件时，该接口才会生效
- 接口有效性依赖文件列表顺序，存在冲突文件时行为不可预期，需谨慎使用

## 常用链接

- Demo：[demo-report-schedule-result-provider](https://code.fanruan.com/hugh/demo-report-schedule-result-provider)

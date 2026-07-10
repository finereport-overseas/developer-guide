# ReportExceptionHandlerProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ReportExceptionHandlerProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ReportExceptionHandlerProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

This interface is primarily used to intercept, handle, and even alert on exceptions that occur during report calculation.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.report.core.ReportLocatedError;
import com.fr.stable.fun.mark.Mutable;

/**
 * Report exception handling interface
 */
public interface ReportExceptionHandlerProvider extends Mutable{
    String MARK_STRING = "ReportExceptionHandlerProvider";
	int CURRENT_LEVEL = 1;

	/**
	 * Handles a ReportLocatedException
	 * @param e Exception with necessary location information (sheet, cell position)
	 */
	void handlerReportLocatedException(ReportLocatedError e);

}

```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
        <ReportExceptionHandlerProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

All plugin-declared report calculation exception handler instances are retrieved via:
`Set<ReportExceptionHandlerProvider> providers = PluginModule.getAgent(PluginModule.ExtraReport).getArray(ReportExceptionHandlerProvider.MARK_STRING);`

In the product, this is applied using ByteBuddy dynamic bytecode instrumentation to inject into the points where calculation exceptions occur. Exceptions are intercepted by `ExceptionLocateInterceptor.intercept` and dispatched to plugin-declared instances via `ReportErrorDispatcher.dispatcherReportLocatedError`.

## 7. Constraints and Notes

When batch template calculation and preview is in use, the `ReportLocatedError` parameter can provide the specific template path, sheet index, cell location, and exception details for the calculation error. In other cases, only the sheet index and cell index are available.

For Decision Reports, only the cell information and exception details are available.

## 8. Useful Links

Demo: [demo-report-exception-handler-provider](https://code.fanruan.com/hugh/demo-report-exception-handler-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

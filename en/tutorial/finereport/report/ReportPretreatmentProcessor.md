# ReportPretreatmentProcessor

| Property | Value |
| --- | --- |
| Interface Type | processor |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ReportPretreatmentProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.report.fun.ReportPretreatmentProcessor) |

## Overview

Performs pre-processing before a report is executed. At this point, the sessionID has been generated but report calculation has not yet begun. This stage is suitable for intercepting, validating, or modifying request parameters.

## Interface Definition

```java
/**
 * Pre-processes a report before execution
 * @since 8.0
 */
public interface ReportPretreatmentProcessor extends Immutable {

    String XML_TAG = "ReportPretreatmentProcessor";

    int CURRENT_LEVEL = 1;

    /**
     * Template pre-processing: the sessionID has been generated, but report calculation has not yet started
     *
     * @param req       HTTP request
     * @param res       HTTP response
     * @param sessionID The sessionID generated for this request
     */
    void process(HttpServletRequest req, HttpServletResponse res, String sessionID);
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-report` tag:

```xml
<extra-report>
    <ReportPretreatmentProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## Example Code

The following example validates the request origin before report execution and blocks access from IPs not on the whitelist:

```java
public class MyReportPretreatmentProcessor implements ReportPretreatmentProcessor {

    @Override
    public void process(HttpServletRequest req, HttpServletResponse res, String sessionID) {
        String remoteIp = req.getRemoteAddr();
        if (!isAllowed(remoteIp)) {
            try {
                res.sendError(HttpServletResponse.SC_FORBIDDEN, "Access denied");
            } catch (IOException e) {
                FineLoggerFactory.getLogger().error(e.getMessage(), e);
            }
        }
    }

    private boolean isAllowed(String ip) {
        // Custom whitelist validation logic
        return ip.startsWith("192.168.");
    }
}
```

## Notes

- When `process()` runs, report calculation has not yet started. It is well-suited for parameter validation, authentication, and logging pre-processing.
- This interface extends `Immutable` and cannot be hot-replaced at runtime.
- To terminate report execution, write a response directly to `res` inside `process()` (e.g., using `sendError`), but be careful to avoid writing to the response more than once, which would cause an exception.

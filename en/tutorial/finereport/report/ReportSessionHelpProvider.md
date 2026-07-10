# ReportSessionHelpProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ReportSessionHelpProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ReportSessionHelpProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

This interface is primarily used to process the report session before report calculation, enabling changes to certain calculation environment parameters, report adjustments, and template modifications. Because the report session is an object that lives within a single report preview request cycle, this interface can also be used to intercept, observe, and intervene in certain logic that has no dedicated interface but does expose session method calls.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.Repository;

/**
 * Interface for special session handling before report calculation
 * Created by zack on 2016/2/24.
 */
public interface ReportSessionHelpProvider extends Mutable{
    String XML_TAG = "ReportSessionHelpProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Special session handling before report calculation
     * (e.g. for fill-in draft saving, injecting the draft data into the session)
     *
     * @param repo repo
     */
    void dealWithSession(Repository repo);

}

```

The `Repository` interface provides access to the browser type, session ID, HTTP request, calculator, resolution, report parameter map, device, font scale, and resource URL resolution. Its primary concrete implementation in report contexts is `ReportRepositoryDeal`.

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
        <ReportSessionHelpProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

All plugin-declared handler instances are retrieved via:
`Set<ReportSessionHelpProvider> sessionHelpProviders = ExtraReportClassManager.getInstance().getArray(ReportSessionHelpProvider.XML_TAG);`

In the product, this is invoked through `AbstractActor#dealWithSessionInfo` after the template has been loaded and a session created, but before the actual content output begins.

## 7. Constraints and Notes

The input parameter is a `Repository` (context) object, which is widely used throughout the report engine for passing context information. The most common concrete type passed in is `ReportRepositoryDeal` (report context). In addition to report session operations, this object provides access to many other objects. However, note that the context is not a single object that persists throughout the entire report lifecycle—it is re-created as needed. The `ReportSessionHelpProvider` context object is only valid at the moment the preview request is initiated; the context re-created during subsequent calculation steps cannot be intercepted by modifying this object.

`ReportSessionHelpProvider` only applies to regular reports and aggregate reports. It does not apply to Decision Reports. Preview modes that are not supported include new fill-in and H5 preview.

In practice, because this interface is triggered immediately after session creation, it can be treated as equivalent to session creation. This makes it a useful monitoring and access event entry point under certain special conditions.

This interface typically couples tightly with internal product object methods that are not part of the public API. These methods may change during upgrades. Developers should plan for this in advance, and thoroughly test and adapt before any update.

## 8. Useful Links

Demo: [demo-report-session-help-provider](https://code.fanruan.com/hugh/demo-report-session-help-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use of these examples is at the user's own risk.

None available.

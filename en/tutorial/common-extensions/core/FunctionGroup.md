# FunctionDefContainer

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.FunctionDefContainer` |

## Background and Use Cases

FanRuan Report provides a rich set of built-in functions for data processing. When built-in functions cannot meet specific business requirements, custom functions can be extended via plugin interfaces. There are two main extension approaches:

- `AbstractFunction`: Extends a single function
- `FunctionDefContainer`: Registers a group of business-related functions in bulk

When multiple related functions need to be extended under a specific business context, `FunctionDefContainer` is recommended for grouped management.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.FunctionDef;

public interface FunctionDefContainer extends Mutable {
    int CURRENT_LEVEL = 1;
    String MARK_STRING = "FunctionGroup";

    FunctionDef[] getFunctionDefs();
    String getGroupName();
}
```

`FunctionDef` contains function name, description, and class name attributes, and supports XML serialization.

`AbstractFunction` is the abstract base class that implements the `evalExpression()` method to handle `Node` arguments, delegating to the abstract method `run(Object[])` and handling error reporting.

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 3.6 | Supported |
| BI | 4.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <FunctionGroup class="your class name"/>
</extra-core>
```

> Note: The registration tag is `<FunctionGroup>`, which differs from the interface class name `FunctionDefContainer` (because `MARK_STRING = "FunctionGroup"`).

## How It Works

When the plugin loads, `ExtraClassManager` converts XML attributes into cached `FunctionDef` objects, completing function definition initialization. Functions are integrated into the calculation system by registering with `DefaultNameSpace`. The lookup priority is: built-in functions → custom extensions → single-function extensions → grouped function extensions.

## Useful Links

- Demo: [demo-function-def-container](https://code.fanruan.com/hugh/demo-function-def-container)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [demo-function-fibonacci](https://code.fanruan.com/fanruan/demo-function-fibonacci)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7615](https://code.fanruan.com/hugh/open-JSD-7615)

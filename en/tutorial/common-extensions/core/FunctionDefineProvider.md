# FunctionDefineProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Module | extra-core |
| Full Class Name | `com.fr.stable.fun.FunctionDefineProvider` |

## Interface Purpose

Provides a plugin-based interface for extending functions.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

import com.fr.plugin.injectable.SpecialLevel;

public interface FunctionDefineProvider extends Level {

    String MARK_STRING = SpecialLevel.FunctionDefineProvider.getTagName();
}
```

### Related Interfaces

When implementing a custom function, extend `AbstractFunction`:

```java
package com.fr.script;

import com.fr.stable.UtilEvalError;
import com.fr.stable.script.Node;

/**
 * Function arguments are evaluated in order.
 * Example: SQRT(ABS(99) + 1)
 * Execution order: first ABS(99)=99, then 99+1=100, finally SQRT(100)=10
 */
public abstract class AbstractFunction extends CalculatorEmbeddedFunction {

    /**
     * Evaluates the function result with the given arguments
     *
     * @param arguments the arguments
     * @return the computed result
     * @throws UtilEvalError thrown if an unresolvable value is encountered during evaluation
     */
    public Object evalExpression(Node[] arguments) throws UtilEvalError {
        Object returnValue;
        if (this.getType() == HA) {
            // Hierarchical coordinate series are not eval'd here
            returnValue = run(arguments);
        } else {
            Object[] args = new Object[arguments.length];
            for (int i = 0; i < arguments.length; i++) {
                args[i] = this.getCalculator().evalValue(arguments[i]);
            }
            returnValue = run(args);
        }

        if (returnValue == Primitive.ERROR_VALUE || returnValue == Primitive.ERROR_NAME) {
            FineLoggerFactory.getLogger().error(
                InterProviderFactory.getProvider().getLocText("Fine-Core_Base_NS_Cell_Formula") + this.toString()
            );
        }

        return returnValue;
    }

    /**
     * Computes the function result
     *
     * Usage example:
     *     SUM sum = new SUM();
     *     System.out.println(sum.run(new Object[]{1, 2, 3}));
     *
     * @param args the function arguments (already evaluated by the calculator)
     * @return the computed result
     */
    public abstract Object run(Object[] args);
}
```

### Plugin Registration

```xml
<extra-core>
    <FunctionDefineProvider class="com.fr.plugin.function.fib.Fibonacci" name="Fibonacci" description="Computes the Fibonacci value."/>
</extra-core>
```

## Interface Example

Example source code: [demo-function-fibonacci](https://code.fanruan.com/fanruan/demo-function-fibonacci)

## Notes

This interface primarily serves as a marker. When implementing a custom function, you still need to:

- **Recommended**: Extend `com.fr.script.AbstractFunction`
- **Not recommended**: Implement the `com.fr.stable.script.Function` interface

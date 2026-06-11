# FunctionDefineProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 所属模块 | extra-core |
| 完整类名 | `com.fr.stable.fun.FunctionDefineProvider` |

## 接口作用

以插件的形式提供函数扩展的接口。

## 接口内容

### 主要接口

```java
package com.fr.stable.fun;

import com.fr.plugin.injectable.SpecialLevel;

public interface FunctionDefineProvider extends Level {

    String MARK_STRING = SpecialLevel.FunctionDefineProvider.getTagName();
}
```

### 关联接口

实现自定义函数时需继承 `AbstractFunction`：

```java
package com.fr.script;

import com.fr.stable.UtilEvalError;
import com.fr.stable.script.Node;

/**
 * 函数参数按顺序执行
 * 例如：SQRT(ABS(99) + 1)
 * 执行顺序为：先算 ABS(99)=99，再算 99+1=100，最后算 SQRT(100)=10
 */
public abstract class AbstractFunction extends CalculatorEmbeddedFunction {

    /**
     * 通过指定的参数，计算函数的结果
     *
     * @param arguments 参数
     * @return 计算结果
     * @throws UtilEvalError 如果在计算中出现无法解析的值，则抛出此异常
     */
    public Object evalExpression(Node[] arguments) throws UtilEvalError {
        Object returnValue;
        if (this.getType() == HA) {
            // 层次坐标系列的不在这边eval
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
     * 计算函数结果
     *
     * 使用示例：
     *     SUM sum = new SUM();
     *     System.out.println(sum.run(new Object[]{1, 2, 3}));
     *
     * @param args 函数参数（已经过算子处理）
     * @return 计算结果
     */
    public abstract Object run(Object[] args);
}
```

### 接口接入

```xml
<extra-core>
    <FunctionDefineProvider class="com.fr.plugin.function.fib.Fibonacci" name="Fibonacci" description="求斐波拉契值。"/>
</extra-core>
```

## 接口示例

示例源码：[demo-function-fibonacci](https://code.fanruan.com/fanruan/demo-function-fibonacci)

## 注意事项

该接口主要起标记作用，实现自定义函数时仍需：

- **推荐**：继承 `com.fr.script.AbstractFunction`
- **不推荐**：实现 `com.fr.stable.script.Function` 接口

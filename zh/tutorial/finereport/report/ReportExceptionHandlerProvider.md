# ReportExceptionHandlerProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ReportExceptionHandlerProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ReportExceptionHandlerProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

该接口主要用于报表计算过程中，对于异常的拦截和处理甚至预警。

## 三、接口介绍


```java
/*
 * Copyright (c) 2001-2014,FineReport Inc, All Rights Reserved.
 */

package com.fr.report.fun;


import com.fr.report.core.ReportLocatedError;
import com.fr.stable.fun.mark.Mutable;

/**
 * 报表异常处理接口
 */
public interface ReportExceptionHandlerProvider extends Mutable{
    String MARK_STRING = "ReportExceptionHandlerProvider";
	int CURRENT_LEVEL = 1;

	/**
	 * 处理ReportLocatedException
	 * @param e 带有必要位置信息(sheet,单元格位置)的异常
	 */
	void handlerReportLocatedException(ReportLocatedError e);

}

```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
        <ReportExceptionHandlerProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

接口通过：Set<ReportExceptionHandlerProvider> providers = PluginModule.getAgent(PluginModule.ExtraReport).getArray(ReportExceptionHandlerProvider.MARK_STRING);获取插件中所有申明的报表计算异常处理接口实例。

在产品中主要通过bytebuddy动态字节码构建技术，对计算产生异常的地方进行注入。通过ExceptionLocateInterceptor.intercept在具体异常产生的时候进行拦截，经由ReportErrorDispatcher.dispatcherReportLocatedError方法调用插件中申明的实例生效。

## 七、特殊限制说明

 如果在批量模板计算和预览的时候，ReportLocatedError入参可以提供具体计算错误的模板路径，sheet序号和单元格，以及具体的异常信息。其他情况只能提供sheet编号和单元格编号。

如果是决策报表就只有单元格和异常信息了。

## 八、常用链接

demo地址：[demo-report-exception-handler-provider](https://code.fanruan.com/hugh/demo-report-exception-handler-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无

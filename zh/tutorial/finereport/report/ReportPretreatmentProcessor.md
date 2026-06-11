# ReportPretreatmentProcessor

| 属性 | 值 |
| --- | --- |
| 接口类型 | processor |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ReportPretreatmentProcessor` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.report.fun.ReportPretreatmentProcessor) |

## 简介

在报表执行前进行预处理。此时 sessionID 已生成，但报表计算尚未开始，可在此阶段对请求参数进行拦截、校验或修改。

## 接口定义

```java
/**
 * 在报表执行前做一个预处理
 * @since 8.0
 */
public interface ReportPretreatmentProcessor extends Immutable {

    String XML_TAG = "ReportPretreatmentProcessor";

    int CURRENT_LEVEL = 1;

    /**
     * 模板预处理：已生成 sessionID，但尚未进行报表计算
     *
     * @param req       HTTP 请求
     * @param res       HTTP 响应
     * @param sessionID 本次请求生成的 sessionID
     */
    void process(HttpServletRequest req, HttpServletResponse res, String sessionID);
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-report` 标签注册实现类：

```xml
<extra-report>
    <ReportPretreatmentProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## 示例代码

以下示例在报表执行前校验请求来源，拒绝非白名单 IP 的访问：

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
        // 自定义白名单校验逻辑
        return ip.startsWith("192.168.");
    }
}
```

## 注意事项

- `process()` 执行时报表计算尚未开始，适合做参数校验、鉴权、日志记录等前置操作。
- 该接口继承 `Immutable`，运行期不可热替换。
- 如需终止报表执行，可在 `process()` 中直接向 `res` 写入响应（如 `sendError`），但需注意避免重复写入导致异常。

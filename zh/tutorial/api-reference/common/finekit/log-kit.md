# LogKit——插件中的日志 Kit

## 源码

[LogKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/log/LogKit.java)

## 说明

LogKit 完全是产品内部日志方法的一个映射，全部来源于 `FineLoggerFactory.getLogger()`。其作用仅仅是通过 kit 将插件与产品解耦，便于提升插件的稳定性。

具体的方法与产品原日志方法含义和用法相同，源码内部也有完整的 Java Doc，此处不再赘述。

## 相关链接

- [finekit 仓库](https://code.fanruan.com/fanruan/finekit)

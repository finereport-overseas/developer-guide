# LogKit — Logging Kit for Plugins

## Source

[LogKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/log/LogKit.java)

## Description

LogKit is a complete mapping of the product's internal logging methods, all delegating to `FineLoggerFactory.getLogger()`. Its sole purpose is to decouple plugins from the product internals through a kit abstraction, making plugins more stable.

The individual methods have the same semantics and usage as the original product logging methods. Complete Javadoc is available in the source code and will not be repeated here.

## Related Links

- [finekit repository](https://code.fanruan.com/fanruan/finekit)

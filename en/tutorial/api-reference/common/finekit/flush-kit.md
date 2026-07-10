# FlushKit — HTTP Response Output Kit

## Source

[FlushKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/web/FlushKit.java)

## Description

FlushKit is used to write HTTP response content to the frontend, and **does not support cross-origin requests**.

The plugin must first serialize the response object to JSON or plain text before using this kit. This is a very low-level and somewhat cumbersome approach. Unless there is no alternative or the scenario is extremely simple, it is not recommended — it tends to make code bloated.

## Related Links

- [finekit repository](https://code.fanruan.com/fanruan/finekit)

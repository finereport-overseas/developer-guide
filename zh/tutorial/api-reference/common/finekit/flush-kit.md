# FlushKit——Web 响应输出 Kit

## 源码

[FlushKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/web/FlushKit.java)

## 说明

FlushKit 主要用于向前端输出 HTTP 响应内容，且**不支持跨域**。

需要插件中先把具体的响应对象序列化为 JSON 对象或者纯文本才行。属于非常基础而且比较麻烦的方式，除非实在没办法了，或者是特别简单的场景，否则不建议使用这种方式，容易让代码变得臃肿。

## 相关链接

- [finekit 仓库](https://code.fanruan.com/fanruan/finekit)

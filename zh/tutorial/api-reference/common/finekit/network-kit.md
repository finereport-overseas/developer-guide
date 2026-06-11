# NetworkKit——请求信息获取 Kit

## 源码

[NetworkKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/net/NetworkKit.java)

## 说明

NetworkKit 完全是产品内部 `NetworkHelper` 对象的映射。其作用仅仅是通过 kit 将插件与产品解耦，便于提升插件的稳定性。

具体的方法与产品原方法含义和用法相同，源码内部也有完整的 Java Doc，此处不再赘述。

主要用于请求信息的获取，用于配合诸如 `Service`、`RequestInterceptor` 这类基础的请求处理接口。最大的用途是**获取请求参数值**。

## 相关链接

- [finekit 仓库](https://code.fanruan.com/fanruan/finekit)

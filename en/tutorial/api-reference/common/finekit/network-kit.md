# NetworkKit — Request Information Kit

## Source

[NetworkKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/net/NetworkKit.java)

## Description

NetworkKit is a complete mapping of the product's internal `NetworkHelper` object. Its sole purpose is to decouple plugins from the product internals through a kit abstraction, making plugins more stable.

The individual methods have the same semantics and usage as the original product methods. Complete Javadoc is available in the source code and will not be repeated here.

NetworkKit is primarily used for retrieving request information, and is intended to be used alongside fundamental request-handling interfaces such as `Service` and `RequestInterceptor`. Its most common use case is **reading request parameter values**.

## Related Links

- [finekit repository](https://code.fanruan.com/fanruan/finekit)

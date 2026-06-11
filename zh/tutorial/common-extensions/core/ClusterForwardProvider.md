# ClusterForwardProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 所属模块 | extra-core |
| 完整类名 | `com.fr.stable.fun.ClusterForwardProvider` |

## 接口作用

插件中如果需要将一系列特殊的请求转发到集群中指定的一个节点上，可以使用该接口。

插件中发出的特定的请求，需要携带了需要转发到同一个节点的信息，实现该接口后，就可以将携带了同一信息的请求转发到同一节点上。其他情况的转发会遵循集群内部默认的转发逻辑，例如按照节点压力转发、随机转发等。

## 接口内容

### 主要接口

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import org.jetbrains.annotations.Nullable;

import javax.servlet.http.HttpServletRequest;

public interface ClusterForwardProvider extends Mutable {

    String MARK_STRING = "ClusterForwardProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 获取转发的目标节点id
     *
     * @param key 集群共享信息key
     * @return 转发目标节点id
     */
    @Nullable
    String getTargetNodeID(String key);

    /**
     * 从请求中获取集群共享信息key
     * <p>
     * 如果请求携带了集群共享信息 key，那么返回共享信息 key
     * 如果未携带，那么返回null
     *
     * @param request req
     * @return 集群共享信息存储key
     * @see #accept(HttpServletRequest)
     */
    String getKey(HttpServletRequest request);

    /**
     * 是否是要进行转发逻辑处理的请求
     * <p>
     * 如果该请求携带了集群共享信息 key
     * 或者该请求是创建集群共享信息的请求，那么返回<code>true</code>;
     * 其余情况返回<code>false</code>
     *
     * @param request req
     * @return 是否是要进行转发处理逻辑的请求
     * @see #getKey(HttpServletRequest)
     */
    boolean accept(HttpServletRequest request);
}
```

### 关联接口

无

### 接口接入

在 `plugin.xml` 中注册：

```xml
<extra-core>
    <ClusterForwardProvider class="com.fr.plugin.excel.submit.web.cluster.ExcelSubmitClusterForwardProvider"/>
</extra-core>
```

## 示例效果

以**批量导入 Excel 插件**为例：

第一步的请求是准备阶段，会生成一个 `processid`，后续的一些当前导入状态的查询、导入的校验以及导入结果的查询等都会携带这个 `processid`；这一系列请求都只能在一个节点上处理。

实现了该接口之后，包含同一个 `processid` 的请求都会被转发到同一节点。

Demo 插件中提供了三个接口：

1. `/req/demo/create`
2. `/req/demo/search`
3. `/req/demo/view`

`search` 接口需要携带 `create` 接口返回的 `id`；携带了 `id` 参数的 `search` 请求总是会被转发到生成该 `id` 的节点，即处理前一个 `create` 请求的节点上。

浏览器中访问 `http://host:port/webroot/decision/url/req/demo/create` 得到返回值：

```json
{
  "id": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9",
  "isCluster": true,
  "clusterId": "M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==",
  "taskName": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ=="
}
```

该请求被集群转发策略（例如随机转发或智能转发）转发到了节点 `M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==` 上。

后续浏览器访问 `http://host:port/webroot/decision/url/req/demo/search?id=4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9` 的请求也会被转发到同一节点，返回值如下：

```json
{
  "id": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9",
  "isCluster": true,
  "clusterId": "M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==",
  "taskName": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ=="
}
```

而浏览器中访问 `http://host:port/webroot/decision/url/req/demo/view` 的请求会被代理（例如 nginx）随机转发到某一节点。

## 接口示例

示例源码：[cluster-req-demo](https://code.fanruan.com/fanruan/cluster-req-demo)

## 注意事项

- 只针对插件中 `RequestPreHandleProvider` 和 `URLAliasProvider` 提供的 URL 路径进行拦截，即 `/url/**` 和 `/plugin/**`

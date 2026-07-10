# ClusterForwardProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Module | extra-core |
| Full Class Name | `com.fr.stable.fun.ClusterForwardProvider` |

## Interface Purpose

Use this interface when a plugin needs to forward a series of special requests to a specific node in the cluster.

Specific requests issued by a plugin carry information that must be forwarded to the same node. Once this interface is implemented, requests carrying the same information are forwarded to the same node. All other forwarding follows the cluster's default routing logic, such as load-based or random forwarding.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import org.jetbrains.annotations.Nullable;

import javax.servlet.http.HttpServletRequest;

public interface ClusterForwardProvider extends Mutable {

    String MARK_STRING = "ClusterForwardProvider";
    int CURRENT_LEVEL = 1;

    /**
     * Get the target node ID for forwarding
     *
     * @param key cluster shared info key
     * @return target node ID
     */
    @Nullable
    String getTargetNodeID(String key);

    /**
     * Extract the cluster shared info key from the request
     * <p>
     * Returns the shared info key if the request carries one;
     * returns null otherwise.
     *
     * @param request req
     * @return cluster shared info storage key
     * @see #accept(HttpServletRequest)
     */
    String getKey(HttpServletRequest request);

    /**
     * Whether this request should be subject to forwarding logic
     * <p>
     * Returns <code>true</code> if the request carries a cluster shared info key
     * or is a request that creates cluster shared info;
     * returns <code>false</code> otherwise.
     *
     * @param request req
     * @return whether forwarding logic should be applied
     * @see #getKey(HttpServletRequest)
     */
    boolean accept(HttpServletRequest request);
}
```

### Related Interfaces

None

### Plugin Registration

Register in `plugin.xml`:

```xml
<extra-core>
    <ClusterForwardProvider class="com.fr.plugin.excel.submit.web.cluster.ExcelSubmitClusterForwardProvider"/>
</extra-core>
```

## Example

Using the **Batch Excel Import plugin** as an example:

The first request is a preparation phase that generates a `processid`. Subsequent requests — such as querying the current import status, validating the import, and retrieving import results — all carry this `processid` and must be handled by the same node.

After implementing this interface, all requests containing the same `processid` are forwarded to the same node.

The demo plugin provides three endpoints:

1. `/req/demo/create`
2. `/req/demo/search`
3. `/req/demo/view`

The `search` endpoint requires the `id` returned by the `create` endpoint. A `search` request carrying an `id` parameter is always forwarded to the node that generated that `id` — i.e., the node that handled the preceding `create` request.

Accessing `http://host:port/webroot/decision/url/req/demo/create` in the browser returns:

```json
{
  "id": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9",
  "isCluster": true,
  "clusterId": "M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==",
  "taskName": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ=="
}
```

This request was forwarded to node `M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==` by the cluster forwarding strategy (e.g., random or intelligent forwarding).

A subsequent browser request to `http://host:port/webroot/decision/url/req/demo/search?id=4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9` is also forwarded to the same node, returning:

```json
{
  "id": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9",
  "isCluster": true,
  "clusterId": "M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ==",
  "taskName": "4da3ca4a-9bb9-5116-dbaf-3d005fc4cab9M+n28J6oNcJMRUJhC+UczHLb2t1xQWXk2jqx2HHpGu3smaPzPw6DDQ=="
}
```

A request to `http://host:port/webroot/decision/url/req/demo/view` in the browser is randomly forwarded to a node by the proxy (e.g., nginx).

## Interface Example

Example source code: [cluster-req-demo](https://code.fanruan.com/fanruan/cluster-req-demo)

## Notes

- Only intercepts URL paths provided by `RequestPreHandleProvider` and `URLAliasProvider` in plugins, i.e., `/url/**` and `/plugin/**`

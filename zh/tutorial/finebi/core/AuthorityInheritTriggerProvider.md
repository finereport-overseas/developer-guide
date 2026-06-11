# AuthorityInheritTriggerProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402881) |

---

# 接口介绍

用于是否启用**自助数据集原始表权限继承开关**的接口(默认不支持这个开关，主题内不展示)


```java
package com.finebi.common.api.provider;

import com.fr.stable.fun.mark.Mutable;

/**
 * @author gaattc
 * @version 6.0
 * Created by gaattc on 2022/8/30
 */
public interface AuthorityInheritTriggerProvider extends Mutable {

    String XML_TAG = "AuthorityInheritTriggerProvider";

    /**
     * 是否启用analysis原始表的权限继承开关
     */
    boolean originTableInheritTriggerEnable();

}
```


```java
package com.finebi.fun.impl;

import com.finebi.common.api.provider.AuthorityInheritTriggerProvider;
import com.finebi.constant.Constants;
import com.fr.stable.fun.mark.API;

/**
 * @author gaattc
 * @version 6.0
 * Created by gaattc on 2022/8/30
 */
@API(level = Constants.Extension.NOT_STACKABLE_API_LEVEL)
public abstract class AbstractAuthorityInheritTriggerProvider implements AuthorityInheritTriggerProvider {

    @Override
    public int currentAPILevel() {
        return Constants.Extension.NOT_STACKABLE_API_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```

# 插件实现


```text
package com.finebi.plugin.bi.authority.inherit;

import com.finebi.fun.impl.AbstractAuthorityInheritTriggerProvider;
import com.fr.intelli.record.Focus;
import com.fr.intelli.record.Original;
import com.fr.record.analyzer.EnableMetrics;

/**
 * @author gaattc
 * @version 6.0
 * Created by gaattc on 2022/8/30
 */
@EnableMetrics
public class ActiveAuthorityInheritTriggerProvider extends AbstractAuthorityInheritTriggerProvider {

    @Override
    @Focus(id = "com.finebi.plugin.bi.authority.inherit.ActiveAuthorityInheritTriggerProvider", text = "BI原始表权限继承开关", source = Original.PLUGIN)
    public boolean originTableInheritTriggerEnable() {
        return true;
    }

}
```

# 使用方式示例

安装插件后使用接口获取插件提供的值：


```java
    private void registerAuthorityInheritTriggerProvider() {
        FineLoggerFactory.getLogger().info("---AuthorityInheritTriggerProvider---init start---");
        Set<AuthorityInheritTriggerProvider> providers = ExtraClassManager.getInstance().getArray(AuthorityInheritTriggerProvider.XML_TAG);
        if (CollectionUtils.isNotEmpty(providers)) {
            providers.stream().findAny().ifPresent(i ->
                    OriginTableInheritTriggerManager.INSTANCE.registerOriginTableInheritTriggerProvider(i.originTableInheritTriggerEnable()));
        }
        FineLoggerFactory.getLogger().info("---AuthorityInheritTriggerProvider---init end---");
    }
```

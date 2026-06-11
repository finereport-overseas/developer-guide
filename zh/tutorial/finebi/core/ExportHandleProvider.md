# ExportHandleProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402895) |

---

## 接口作用

希望在导出操作之前，加入一些自定义操作，可以用这个接口实现。比如导出的文件进行自定义加密。

****⚠️ 注：该接口为流式接口，数据量较大，应避免将数据放入内存，否则存在宕机风险！！****

## 接口内容

### 主要接口


```java
package com.finebi.dashboard.impl.service.export;

import com.finebi.dashboard.api.service.export.ExportContext;
import com.fr.stable.fun.mark.Mutable;

import java.io.OutputStream;

/**
 * 提供导出流修改接口
 * Created by Hiram on 2018/11/14.
 */
public interface ExportHandleProvider extends Mutable {
    String XML_TAG = "ExportHandleProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 导出流修改
     *
     * @param originalOutputStream 原始导出流
     * @param type                 导出类型
     * @return 处理后的流
     */
    OutputStream handleStream(OutputStream originalOutputStream, ExportType type);

    /**
     * 导出流修改
     *
     * @param originalOutputStream 原始导出流
     * @param exportContext        导出上下文
     * @return
     */
    OutputStream handleStream(OutputStream originalOutputStream, ExportContext exportContext);
}


```

## 接口接入


```xml
<extra-core>
    <ExportHandleProvider class="插件自定义实现包全名"/>
</extra-core>
```

## 提供给插件的抽象类


```java
package com.finebi.provider.api.export;

import com.finebi.dashboard.impl.service.export.ExportHandleProvider;
import com.finebi.dashboard.impl.service.export.ExportType;
import com.fr.stable.fun.mark.API;

import java.io.OutputStream;

/**
 * 计算导出excel的大小
 * Created by Hiram on 2018/11/15.
 */
@API(level = ExportHandleProvider.CURRENT_LEVEL)
public abstract class AbstractExportHandleProvider implements ExportHandleProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    @Deprecated
    public OutputStream handleStream(OutputStream originalOutputStream, ExportType type) {
        return originalOutputStream;
    }

}


```

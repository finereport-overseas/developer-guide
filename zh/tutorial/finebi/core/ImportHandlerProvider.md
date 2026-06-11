# ImportHandlerProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402891) |

---

## 接口作用

希望在导如操作之前，加入一些自定义操作，可以用这个接口实现。比如导如的文件进行自定义解密。可以和[ExportHandleProvider接口](https://kms.fineres.com/pages/viewpage.action?pageId=45369044)配合使用

## 接口内容

### 主要接口


```java
package com.finebi.common.excel.api.handler;

import com.fr.stable.fun.mark.Mutable;

import java.io.InputStream;

/**
 * 对导入流进行预处理
 */
public interface ImportHandlerProvider extends Mutable {

    String XML_TAG = "ImportHandlerProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 对导入流进行预处理
     *
     * @param inputStream 输入流
     * @param context     处理参数
     * @return 处理之后的流
     */
    InputStream handleStream(InputStream inputStream, ImportContext context);

}


```

## 接口接入


```xml
<extra-core>
    <ImportHandlerProvider class="插件自定义实现包全名"/>
</extra-core>
```

## 提供给插件的抽象类


```java
package com.finebi.provider.api.imports;

import com.finebi.common.excel.api.handler.ImportContext;
import com.finebi.common.excel.api.handler.ImportHandlerProvider;
import com.fr.stable.fun.mark.API;

import java.io.InputStream;

/**
 * 对导入流进行预处理
 * Created by Hiram on 2018/11/15.
 */
@API(level = ImportHandlerProvider.CURRENT_LEVEL)
public abstract class AbstractImportHandlerProvider implements ImportHandlerProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    public InputStream handleStream(InputStream inputStream, ImportContext context) {
        return inputStream;
    }
}


```

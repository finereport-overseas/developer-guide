# CustomComponentProvider

`CustomComponentProvider` 支持插件自定义组件（图表），是 FineBI 6.0 提供的自定义图表注册接入点。

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| BI | 6.0.13+ | 支持 |

## 插件注册

```xml
<extra-core>
    <CustomComponentProvider class="your class name"/>
</extra-core>
```

## 后端接口

```java
package com.finebi.provider.api.component;

import com.finebi.common.context.OperationContext;
import com.finebi.provider.api.component.data.DataModel;
import com.fr.common.annotations.Open;
import com.fr.stable.fun.mark.Mutable;
import com.fr.web.struct.AssembleComponent;

import java.util.List;

/**
 * 插件自定义组件（图表）的注册点
 *
 * @author Jimmy.Zheng
 * @since 6.0
 */
@Open
public interface CustomComponentProvider extends Mutable {
    String XML_TAG = "CustomComponentProvider";

    int CURRENT_LEVEL = 1;

    /** 获取组件的名称 */
    String getName();

    /** 获取组件的类型，建议英文 */
    String getType();

    /** 获取组件的图标 */
    String getIcon();

    /** 获取组件的预览大图标 */
    String getPreviewIcon();

    /** 获取组件编辑界面的 html */
    String getEditPageHTML(OperationContext context);

    /** 获取组件编辑界面的 css 和 js */
    AssembleComponent editClient(OperationContext context);

    /** 获取组件预览界面的 html */
    String getPreviewPageHTML(OperationContext context);

    /** 获取组件预览界面的 css 和 js */
    AssembleComponent previewClient(OperationContext context);

    String config();

    /**
     * 返回是否需要进行自定义数据处理
     *
     * @param customComponentContext 自定义图表相关的上下文信息，目前包含前端传的查询配置
     */
    boolean needDataProcess(CustomComponentContext customComponentContext);

    /**
     * 对 BI 计算后即将返回前端的数据进行自定义处理
     *
     * @param dataModels             数据模型
     * @param customComponentContext 自定义图表相关的上下文信息，目前包含前端传的查询配置
     * @return 处理过的数据模型
     */
    List<DataModel> process(List<DataModel> dataModels, CustomComponentContext customComponentContext);
}
```

## 前端接口

```typescript
import { BIPluginAction, BIPluginMessage } from '@webui/bi/plugin/bi.plugin.constant';

export class BIPluginClient {
    private connID: string | null = null;

    /**
     * 初始化插件客户端
     * - data：如果是保存过的，这里直接填入开发者保存的信息，新建的话这里为 null
     * - config：配置信息，比如提供所在模板等环境类信息
     * - saveSessionCallback：保存回调函数，入参为保存的 json 对象，返回 Promise
     * - closeSessionCallback：调用该接口会结束当前页面会话，把主动权交给 BI
     */
    public init(
        render: (
            data: any,
            config: any,
            saveSessionCallback: (data: any) => Promise<any>,
            closeSessionCallBack: () => void,
            extensionCallback?: (action: string, value: any) => void
        ) => void
    ): void {
        addEventListener('message', (event: MessageEvent) => {
            if (event.source === window.parent && event.data) {
                const eventData = JSON.parse(event.data);
                if (!this.connID && eventData.action === BIPluginAction.INIT) {
                    this.connID = eventData.connID;
                } else {
                    if (this.connID === eventData.connID && eventData.action === BIPluginAction.RENDER) {
                        const { data, config } = eventData;
                        render(
                            data,
                            config,
                            this.saveSessionCallback.bind(this),
                            this.closeSessionCallback.bind(this),
                            this.extensionCallback.bind(this)
                        );
                    }
                }
            }
        });
    }

    private sendMessage(action: BIPluginAction, data: any, config?: any) {
        if (this.connID) {
            const message: BIPluginMessage = { connID: this.connID, action, data, config };
            parent.postMessage(JSON.stringify(message), '*');
        }
    }

    private saveSessionCallback(data: any): Promise<any> {
        return new Promise(resolve => {
            const saveListener = (event: MessageEvent) => {
                if (event.source === window.parent && event.data) {
                    const eventData = JSON.parse(event.data);
                    if (this.connID === eventData.connID && eventData.action === BIPluginAction.SAVE_COMPLETE) {
                        removeEventListener('message', saveListener);
                        resolve(eventData.data);
                    }
                }
            };
            this.sendMessage(BIPluginAction.SAVE, data);
            addEventListener('message', saveListener);
        });
    }

    private closeSessionCallback(): void {
        this.sendMessage(BIPluginAction.CLOSE, null);
    }

    private extensionCallback(action: string, value: any) {
        this.sendMessage(action, value);
    }
}

// @ts-ignore
window.BIPlugin = BIPluginClient;
```

## 图表配置

图表配置分为两部分：**默认配置**和**自定义配置**。

自定义配置分为：
- 组件样式和图表属性区域的自定义配置（`config.json` 中配置，支持固定的几种类型）
- 图表编辑区域的自定义配置（可理解为自定义的 JSON 配置，配置界面可以自定义）

## 图表数据

图表数据主要存在 `dataModel` 中，主要内容有：
- **fields**：字段数据
- **colData**：按列存储的二维表数据

数据格式转化：日期字段格式转换后的数据、数值字段格式配置，均存放在 `dataModel` 的 `fields` 配置里。

## 原理说明

注册逻辑的实现主要在 `FinePluginService` 的实现类中：

- `FinePluginService#getCustomComponentPage`：获取自定义图表预览界面 html
- `FinePluginService#getAllCustomComponentInfo`：获取自定义图表图标、name 等信息

## 特殊限制

目前尚未支持移动端、后台导出、数据脱敏等场景。

## 相关链接

- [plugin-bi-custom-chart-demo](https://code.fanruan.com/Zhanying/plugin-bi-custom-chart-demo)

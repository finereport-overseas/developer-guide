# CustomComponentProvider

`CustomComponentProvider` enables plugins to register custom components (charts). It is the custom chart registration extension point provided by FineBI 6.0.

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| BI | 6.0.13+ | Supported |

## Plugin Registration

```xml
<extra-core>
    <CustomComponentProvider class="your class name"/>
</extra-core>
```

## Backend Interface

```java
package com.finebi.provider.api.component;

import com.finebi.common.context.OperationContext;
import com.finebi.provider.api.component.data.DataModel;
import com.fr.common.annotations.Open;
import com.fr.stable.fun.mark.Mutable;
import com.fr.web.struct.AssembleComponent;

import java.util.List;

/**
 * Registration point for plugin-defined custom components (charts)
 *
 * @author Jimmy.Zheng
 * @since 6.0
 */
@Open
public interface CustomComponentProvider extends Mutable {
    String XML_TAG = "CustomComponentProvider";

    int CURRENT_LEVEL = 1;

    /** Returns the component name */
    String getName();

    /** Returns the component type (English recommended) */
    String getType();

    /** Returns the component icon */
    String getIcon();

    /** Returns the large preview icon for the component */
    String getPreviewIcon();

    /** Returns the HTML for the component's edit page */
    String getEditPageHTML(OperationContext context);

    /** Returns the CSS and JS for the component's edit page */
    AssembleComponent editClient(OperationContext context);

    /** Returns the HTML for the component's preview page */
    String getPreviewPageHTML(OperationContext context);

    /** Returns the CSS and JS for the component's preview page */
    AssembleComponent previewClient(OperationContext context);

    String config();

    /**
     * Returns whether custom data processing is required.
     *
     * @param customComponentContext Context information for the custom chart, currently includes the query config passed from the frontend
     */
    boolean needDataProcess(CustomComponentContext customComponentContext);

    /**
     * Applies custom processing to the data that BI has computed and is about to return to the frontend.
     *
     * @param dataModels             Data models
     * @param customComponentContext Context information for the custom chart, currently includes the query config passed from the frontend
     * @return Processed data models
     */
    List<DataModel> process(List<DataModel> dataModels, CustomComponentContext customComponentContext);
}
```

## Frontend Interface

```typescript
import { BIPluginAction, BIPluginMessage } from '@webui/bi/plugin/bi.plugin.constant';

export class BIPluginClient {
    private connID: string | null = null;

    /**
     * Initialize the plugin client.
     * - data: For a previously saved component, pass the developer-saved data here; for a new component, this is null.
     * - config: Configuration information, e.g., environmental info such as the current template.
     * - saveSessionCallback: Save callback function; the parameter is the JSON object to save, returns Promise.
     * - closeSessionCallback: Calling this interface ends the current page session and returns control to BI.
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

## Chart Configuration

Chart configuration is divided into two parts: **default configuration** and **custom configuration**.

Custom configuration includes:
- Custom configuration for the component style and chart property panels (configured in `config.json`, supports a fixed set of types).
- Custom configuration for the chart edit area (essentially a custom JSON configuration with a fully customizable UI).

## Chart Data

Chart data is primarily stored in `dataModel` and contains:
- **fields**: Field data
- **colData**: Two-dimensional table data stored by column

Data format conversion: date field format conversions and numeric field format settings are both stored in the `fields` configuration of `dataModel`.

## Implementation Details

The registration logic is primarily implemented in the `FinePluginService` implementation class:

- `FinePluginService#getCustomComponentPage`: Retrieves the HTML for the custom chart preview page.
- `FinePluginService#getAllCustomComponentInfo`: Retrieves the icon, name, and other information for custom charts.

## Known Limitations

Mobile devices, background export, and data masking scenarios are not currently supported.

## Related Links

- [plugin-bi-custom-chart-demo](https://code.fanruan.com/Zhanying/plugin-bi-custom-chart-demo)

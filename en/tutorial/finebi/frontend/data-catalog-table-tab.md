# Data Catalog Table Preview Tab Extension Interface — Usage Example

> **Minimum supported version**: FineBI 7.0.13.1

This interface is used to add or adjust Tabs in the **table detail page** and **search result table preview** of the data catalog. It reuses the project's existing Provider mechanism, so no separate FDI extension registration is required.

## 1. Minimal Injection Example

Run the following code after the common Provider bridge of the data catalog page finishes loading. When debugging in the browser, select the frame that actually hosts the data catalog.

```js
var disposeTableInfoTab;

(function () {
  // Defined outside transform to keep a stable function reference.
  function renderInfo(container, context) {
    const content = document.createElement("pre");
    content.style.cssText = "margin:0;white-space:pre-wrap;overflow-wrap:anywhere";
    content.textContent = JSON.stringify(
      context,
      (_key, value) => value === undefined ? "[undefined]" : value,
      2
    );
    container.style.overflow = "auto";
    container.append(content);

    return () => content.remove();
  }

  BI.config("fdi.provider.catalog.table.tab", provider => {
    disposeTableInfoTab = provider.inject({
      id: "table-info-demo",
      transform: (_context, tabs) => [
        ...tabs,
        {
          id: "table-info-demo:info",
          label: "Extension Info",
          render: renderInfo,
        },
      ],
    });
  });
})();
```

The new "Extension Info" Tab displays the complete context. The `[undefined]` above is only used to display undefined fields and does not modify the original object.

To revoke when no longer needed:

```js
disposeTableInfoTab();
```

> **Note (loading order)**: Due to the page's JS loading order, `BI.config("fdi.provider.catalog.table.tab", ...)` may execute before the Provider bridge is ready, so the injection might not take effect. It is recommended to wrap the injection logic inside the `window` `load` event (the `renderFineuiTab` in the example is your own render function; see section 3 for how to define it):

```js
window.addEventListener("load", function () {
    BI.config("fdi.provider.catalog.table.tab", provider => {
        provider.inject({
            id: "fineui-tab-demo",
            transform: (_context, tabs) => [
                ...tabs,
                {
                    id: "fineui-tab-demo:info",
                    label: BI.i18nText("Plugin-Field_Comment_Tab"),
                    render: renderFineuiTab
                },
            ],
        });
    });
});
```

## 2. Configuration and Parameters

### Registration Configuration

```ts
provider.inject({
  id: "my-plugin",
  transform: (context, tabs) => nextTabs,
});
```

| Field | Description |
| --- | --- |
| `id` | Unique identifier of the current plugin configuration. A new configuration with the same ID replaces the old one while preserving the combined order. |
| `transform(context, tabs)` | Synchronously returns the final list. You may add, reorder, hide, rename, or replace items; do not mutate the passed array or objects. |

Multiple plugins process the list in registration order; each subsequent plugin receives the result of the previous one. Runtime registration, replacement, and revocation cause already-open previews to recompute the list. Ensure the Tab IDs are unique and return configurations that conform to the contract; the Provider performs no extra per-item validation or exception fallback.

### Tab Description

| Field | Description |
| --- | --- |
| `id` | For new items use `pluginConfigId:tabName`, e.g. `my-plugin:info`; when adjusting built-in items, keep their original IDs. |
| `label` | Title; supports string or ReactNode. When injecting via FineUI or plain JavaScript you can pass a string directly. |
| `render` | Content mount function. Required for new items; built-in items keep showing their original content when not provided. |

The built-in IDs for table preview are `preview`, `usage`, `update`, `cache`, `relationView`. The actually available list is affected by conditions such as the engine, table configuration, and feature switches, so you should adjust based on the received tabs rather than assuming every item exists.

### context

Both transform and render receive the context of the current preview:

| Field | Description |
| --- | --- |
| `scene` | `detail` means table detail, `search-preview` means search preview. |
| `table` | The current table object. `name` is the internal identifier, `transferName` is the display name; other fields depend on the current table type. |
| `detail` | The current table detail, which may not be loaded yet and can be undefined. |
| `authInfo` | The original permission object, passed through directly without enum conversion. Contains `use` and `field`. |

`authInfo.use` and `authInfo.field` use the original AuthInfo enum values:

| Enum | Value | Meaning |
| --- | --- | --- |
| `AuthInfo.APPLYING` | 1 | Applying |
| `AuthInfo.HAS_AUTHORITY` | 2 | Has authority |
| `AuthInfo.NO_AUTHORITY` | 3 | No authority |

Project source code still uses the original enums from `@fdi/asset-manager`. The context does not include the loading/error state of permission requests; the initial value does not mean the server has finished authorization, and the plugin's business requests must still follow the original permission rules.

## 3. render: Mount React or FineUI Yourself

```ts
render(container, context, control): void | (() => void)
```

- `container`: the HTMLElement provided by the host, in which the plugin mounts its content.
- `context`: the current table context described above.
- `control.activateTab(id)`: switch to the Tab in the current list; returns true on success, false if the target does not exist.
- Return value: an optional cleanup function used to unmount the React root, destroy a widget, cancel subscriptions, etc.

render does not directly return a React node or FineUI config. The host does not recognize the rendering framework.

### React

The following is plugin module source code that must be built through the plugin project before loading; do not paste import-bearing code directly into the browser Console.

```js
import { createElement } from "react";
import { createRoot } from "react-dom/client";

function renderReactTab(container, context, control) {
  const root = createRoot(container);
  root.render(
    createElement(
      "div",
      undefined,
      createElement("h3", undefined, context.table.transferName),
      createElement("pre", undefined, JSON.stringify(context, undefined, 2)),
      createElement(
        "button",
        { onClick: () => control.activateTab("preview") },
        "Back to Data Preview"
      )
    )
  );

  return () => root.unmount();
}

BI.config("fdi.provider.catalog.table.tab", provider => {
  provider.inject({
    id: "react-tab-demo",
    transform: (_context, tabs) => [
      ...tabs,
      { id: "react-tab-demo:info", label: "React Example", render: renderReactTab },
    ],
  });
});
```

You can also use JSX: `root.render(<MyTab context={context} />)`. An independent root does not automatically inherit the host React Context; the plugin must provide any Providers it needs.

### FineUI

```js
(function () {
  function renderFineuiTab(container, context) {
    const labelElement = document.createElement("div");
    const contextElement = document.createElement("pre");
    contextElement.style.whiteSpace = "pre-wrap";
    contextElement.textContent = JSON.stringify(context, undefined, 2);
    container.style.overflow = "auto";
    container.append(labelElement, contextElement);

    const widget = BI.createWidget({
      type: "bi.label",
      element: BI.$(labelElement),
      text: context.table.transferName,
      textAlign: "left",
      height: 32,
    });

    return () => {
      widget.destroy();
      contextElement.remove();
    };
  }

  BI.config("fdi.provider.catalog.table.tab", provider => {
    provider.inject({
      id: "fineui-tab-demo",
      transform: (_context, tabs) => [
        ...tabs,
        { id: "fineui-tab-demo:info", label: "FineUI Example", render: renderFineuiTab },
      ],
    });
  });
})();
```

When using your own widget, replace the type and corresponding config, e.g. pass `value: context.table` to the widget.

## 4. List Customization Examples

The following are transform implementations that can be placed in the inject config. `renderInfo` uses the stable mount function defined in section 1.

Append only on the detail page:

```js
transform: (context, tabs) => {
  if (context.scene !== "detail") return tabs;

  return [...tabs, {
    id: "my-plugin:info",
    label: "Extension Info",
    render: renderInfo,
  }];
}
```

Insert after the data preview:

```js
transform: (_context, tabs) => {
  const index = tabs.findIndex(tab => tab.id === "preview");
  const position = index < 0 ? tabs.length : index + 1;
  const item = { id: "my-plugin:info", label: "Extension Info", render: renderInfo };

  return [...tabs.slice(0, position), item, ...tabs.slice(position)];
}
```

Hide an item, rename a title, or replace content:

```js
// Hide "usage" analysis.
transform: (_context, tabs) => tabs.filter(tab => tab.id !== "usage")

// Rename the preview title, keep the original content.
transform: (_context, tabs) => tabs.map(tab =>
  tab.id === "preview" ? { ...tab, label: "Table Data" } : tab
)

// Replace the preview content.
transform: (_context, tabs) => tabs.map(tab =>
  tab.id === "preview" ? { ...tab, render: renderInfo } : tab
)
```

The above are independent configuration fragments; do not put multiple transforms with the same name into one object. The user-visible text of actual plugins should use their own internationalization resources.

## 5. Mounting, Caching, and Revocation

- render is called after the content component mounts; when the context or render function reference changes, the cleanup function is called first, then it remounts. Therefore it is recommended to define render outside transform.
- Caching follows the host Tab's destroyInactive; keepAlive is not provided. Normal Tab switching with unchanged context does not rebuild retained content.
- Switching tables, detail, or permission context changes may trigger remounting, and the plugin's internal state may be reset.
- Cleanup runs when a Tab is removed or the page is unloaded. Only operate on the passed container and its children; do not delete host nodes.
- After the currently selected item is removed, the host falls back to preview, the first item, or an empty state in order.
- No host refresh method is provided, nor is there an independent update/destroy instance protocol.

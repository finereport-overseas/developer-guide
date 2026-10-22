# 数据目录表预览 Tab 扩展接口使用示例

> **最低支持版本**：FineBI 7.0.13.1

本接口用于在数据目录的**表详情页**和**搜索结果表预览**中新增或调整 Tab。沿用项目已有的 Provider 机制，不需要单独注册 FDI 扩展。

## 1. 最小注入示例

在数据目录页面的 common Provider 桥接加载完成后执行以下代码。浏览器调试时，需要选择实际承载数据目录的 frame。

```js
var disposeTableInfoTab;

(function () {
  // 定义在 transform 外，保持函数引用稳定。
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
          label: "扩展信息",
          render: renderInfo,
        },
      ],
    });
  });
})();
```

新增的“扩展信息”Tab 会展示完整 context。上面的 `[undefined]` 仅用于展示未定义字段，不修改原对象。

不再需要时撤销：

```js
disposeTableInfoTab();
```

> **注意（加载顺序）**：由于页面 JS 的加载顺序问题，`BI.config("fdi.provider.catalog.table.tab", ...)` 可能在 Provider 桥接完成之前就执行，导致注入不生效。建议将注入逻辑包在 `window` 的 `load` 事件中（示例中的 `renderFineuiTab` 为你自定义的渲染函数，定义方式见第 3 节）：

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

## 2. 配置与参数

### 注册配置

```ts
provider.inject({
  id: "my-plugin",
  transform: (context, tabs) => nextTabs,
});
```

| 字段 | 说明 |
| --- | --- |
| `id` | 当前插件配置的唯一标识。相同 ID 的新配置会替换旧配置，并保留组合顺序。 |
| `transform(context, tabs)` | 同步返回最终列表。可新增、排序、隐藏、改名或替换内容；不修改传入数组和对象。 |

多个插件按照注册顺序处理列表，后一个插件收到前一个插件的处理结果。运行时注册、替换和撤销会使已打开的预览重新计算列表。请保证 Tab ID 唯一并返回符合约定的配置；Provider 不做额外的逐项校验或异常回退。

### Tab 描述

| 字段 | 说明 |
| --- | --- |
| `id` | 新增项使用 `插件配置ID:页签名称`，例如 `my-plugin:info`；调整内置项时保留其原 ID。 |
| `label` | 标题，支持字符串或 ReactNode。使用 FineUI 或普通 JavaScript 注入时可直接传字符串。 |
| `render` | 内容挂载函数。新增项必填；内置项未提供时继续显示原有内容。 |

表预览的内置 ID 有 `preview`、`usage`、`update`、`cache`、`relationView`。实际可用列表受引擎、表配置、功能开关等条件影响，应基于收到的 tabs 调整，不假定每一项都存在。

### context

transform 和 render 接收当前预览的上下文：

| 字段 | 说明 |
| --- | --- |
| `scene` | `detail` 表示表详情，`search-preview` 表示搜索预览。 |
| `table` | 当前表对象。`name` 是内部标识，`transferName` 是显示名称；其他字段以当前表类型为准。 |
| `detail` | 当前表详情，可能尚未加载，为 undefined。 |
| `authInfo` | 原有权限对象，直接传入，不转换枚举值。包含 `use` 和 `field`。 |

`authInfo.use` 和 `authInfo.field` 使用原有 AuthInfo 枚举值：

| 枚举 | 值 | 含义 |
| --- | --- | --- |
| `AuthInfo.APPLYING` | 1 | 申请中 |
| `AuthInfo.HAS_AUTHORITY` | 2 | 有权限 |
| `AuthInfo.NO_AUTHORITY` | 3 | 无权限 |

项目源码仍从 `@fdi/asset-manager` 使用原有枚举。上下文不包含权限请求的 loading/error 状态；初始值不表示服务端已完成授权，插件的业务请求仍需遵循原权限规则。

## 3. render：自行挂载 React 或 FineUI

```ts
render(container, context, control): void | (() => void)
```

- `container`：宿主提供的 HTMLElement，插件在其中挂载内容。
- `context`：上文的当前表上下文。
- `control.activateTab(id)`：切换到当前列表中的 Tab，成功返回 true，目标不存在返回 false。
- 返回值：可选清理函数，用于卸载 React root、销毁 widget、取消订阅等。

render 不直接返回 React 节点或 FineUI 配置。宿主不识别渲染框架。

### React

以下是插件模块源码，需要通过插件工程构建后加载，不能将带 import 的代码直接粘贴到浏览器 Console。

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
        "返回数据预览"
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
      { id: "react-tab-demo:info", label: "React 示例", render: renderReactTab },
    ],
  });
});
```

也可以使用 JSX：`root.render(<MyTab context={context} />)`。独立 root 不会自动继承宿主 React Context，插件需要的 Provider 由插件自行提供。

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
        { id: "fineui-tab-demo:info", label: "FineUI 示例", render: renderFineuiTab },
      ],
    });
  });
})();
```

使用自己的 widget 时，替换 type 和对应配置即可，例如向 widget 传入 `value: context.table`。

## 4. 列表定制示例

下面是可放入 inject 配置的 transform 写法。`renderInfo` 使用第 1 节定义的稳定挂载函数。

仅在详情页追加：

```js
transform: (context, tabs) => {
  if (context.scene !== "detail") return tabs;

  return [...tabs, {
    id: "my-plugin:info",
    label: "扩展信息",
    render: renderInfo,
  }];
}
```

插入到数据预览后面：

```js
transform: (_context, tabs) => {
  const index = tabs.findIndex(tab => tab.id === "preview");
  const position = index < 0 ? tabs.length : index + 1;
  const item = { id: "my-plugin:info", label: "扩展信息", render: renderInfo };

  return [...tabs.slice(0, position), item, ...tabs.slice(position)];
}
```

隐藏某项、修改标题或替换内容：

```js
// 隐藏使用分析。
transform: (_context, tabs) => tabs.filter(tab => tab.id !== "usage")

// 修改预览标题，保留原内容。
transform: (_context, tabs) => tabs.map(tab =>
  tab.id === "preview" ? { ...tab, label: "表数据" } : tab
)

// 替换预览内容。
transform: (_context, tabs) => tabs.map(tab =>
  tab.id === "preview" ? { ...tab, render: renderInfo } : tab
)
```

以上为相互独立的配置片段，不应将多个同名 transform 属性同时写进一个对象。实际插件的用户可见文案应使用自己的国际化资源。

## 5. 挂载、缓存与撤销

- render 在内容组件挂载后调用；context 或 render 函数引用变化时，先调用清理函数，再重新挂载。因此建议将 render 定义在 transform 外。
- 缓存沿用宿主 Tab 的 destroyInactive，不提供 keepAlive。普通 Tab 切换且上下文不变时，不额外重建已保留的内容。
- 切表、详情或权限上下文变化可能触发重新挂载，插件内部状态可能重置。
- 移除 Tab 或卸载页面时执行清理。只操作传入容器及其子节点，不删除宿主节点。
- 当前选中项被移除后，宿主依次回退到 preview、首项或空态。
- 不提供宿主刷新方法，也不提供独立 update/destroy 实例协议。

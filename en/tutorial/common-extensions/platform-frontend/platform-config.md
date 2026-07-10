# Platform Configuration

## BI.config

Use the `BI.config` method to configure the extension points provided by the platform. The decision-making platform exposes a series of providers for configuring extensions. The calling pattern is as follows:

```js
BI.config("dec.provider.xxx", function (provider) {
    // Call the methods provided by the provider to register extensions
    provider.xxx()
});
```

Example: extending the management system with a custom node

```js
// Management system
BI.config("dec.provider.management", function (provider) {
    provider.inject({
        modules: [
            {
                value: "custom_manage",
                id: "decision-management-custom-manage",
                text: "Custom Management",
                cardType: "my.custom_manage",
                cls: "setting-font",
                dev: true
            }
        ]
    });
});
```

`BI.config` can also be used to configure components by modifying their `options`.

Example: replacing the classic-mode sidebar directory tree component with a custom component

```js
BI.config("dec.frame.classic.aside.entry_tree", function (options) {
    options.type = "your.onw.tre"; // Replace the component type with a custom one
    return options;
});
```

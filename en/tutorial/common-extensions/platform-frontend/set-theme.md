# Set Theme

The platform theme framework configuration includes layout strategy configuration and layout information configuration.

The platform ships with two built-in modes: `classic` and `modern`. You can initialize from either of these modes for customization. Developers are free to choose the level of customization — extending a built-in mode is recommended. If you have an in-depth understanding of FineUI, you can also define a fully custom mode.

## Layout Strategy

The layout strategy configures the overall layout of the platform. The default layout uses absolute positioning in a "top, left, right" pattern, divided into a `header`, a `menu` bar, and a `body` main content area:

```js
type: "bi.absolute",
items: [
    {
        el: {
            type: "bi.absolute",
            items: [
                {
                    el: {
                        type: "dec.body"
                    },
                    top: 0, bottom: 0, right: 0,
                    left: layoutConfig.west.invisible ? 0 : layoutConfig.west.width
                }, {
                    el: {
                        type: "dec.menu",
                        width: layoutConfig.west.width, // width, default 70
                        invisible: layoutConfig.west.invisible
                    },
                    top: 0, bottom: 0,
                    left: 0
                }
            ]
        },
        top: layoutConfig.north.invisible ? 0 : layoutConfig.north.height, left: 0, right: 0, bottom: 0
    }, {
        el: {
            type: "dec.header",
            height: layoutConfig.north.height,
            invisible: layoutConfig.north.invisible
        },
        height: layoutConfig.north.height,
        top: 0, left: 0, right: 0
    }
]
```

To configure the layout strategy:

```js
BI.config("dec.provider.theme", function (provider) {
    provider.inject({
        layoutStrategy: {
            //  custom layout strategy
        }
    });
});
```

## Layout Information

Layout information configures certain properties (width, height, etc.) of the built-in layout strategy. The full configuration object is as follows:

```js
{
    // Frame layout configuration properties
    config4Frame: {
        north: {  // Top bar
            height: 40,
            invisible: false
        },
        west: {  // Menu
            width: 70,
            invisible: false
        },
        center: {
            left: {  // Left panel of the main content area
                width: 240,
                maxSize: 500,
                minSize: 240
            }
        }
    }
}
```

Configuration example: widen the directory panel and increase the maximum width

```js
BI.config("dec.provider.layout", function (provider) {
    provider.inject({
        layoutConfig: {
            center: {
                left: {
                    width: 340,
                    maxSize: 800
                }
            }
        }
    });
});
```

## Customize the Base Mode

You can set the `base` property to specify the base mode. If you do not want to use a built-in mode and prefer full customization, set `base` to an empty string and then specify the provider key:

```js
BI.config("dec.provider.theme", function (provider) {
    provider.inject({
        base: "",
        provider: "my.own.provider"
    });
});

var Provider = function () {

    this.$get = function () {
        return BI.inherit(BI.OB, {

            initConfig: function (config) {
                // Initialize custom settings — this example configures the directory node in classic theme mode
                BI.config("dec.constant.menu.items", function (items) {
                    BI.some(items, function (index, item) {  // some method is similar to lodash's some
                        if (item.value === "directory") {
                            item.cardType = "dec.frame.classic";
                            return true;
                        }
                    });
                    return items;
                });
            }
        });
    };
};
BI.provider("my.own.provider", Provider);
```

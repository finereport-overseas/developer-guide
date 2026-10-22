# 连接器数据源读取插件接口（前端）

## 接口 - Materials

| 接口 | 说明 | 参数 |
| --- | --- | --- |
| `createFlowNodeDefaultConfig4Read` | 创建输入算子的默认配置 | `{ nodeType: string; // 算子类型, datasourceType: string; // 数据源类型, defaultConfig: object; // 默认配置 }` |
| `createFlowNodeConfigTabsMap4DataSource` | 创建数据来源的配置 tabs | `{ render: ({ inputNodeProvider, setInputNodeProvider, visitor }) => ReactNode; // tab 渲染器 }` |
| `createFlowNodeClass4Read` | 创建输入算子 | 同 `FDL.Materials.createFlowNodeClass` |

## 接口 - Providers

| 接口 | 说明 | 参数 |
| --- | --- | --- |
| `FlowNodeClassProvider.register` | 注册算子 | `node: object; // 算子` |
| `FlowNodeConfigTabProvider.register` | 注册算子配置 tab | `datasourceType: string; // 数据源类型` + `{ flowNode4Read: object; // 数据源对应的输入算子, configTabsMap4DataSource: object; // 数据源对应的数据来源配置 tab }` |

## 示例

```js
FDL.PluginManager.registerPlugin({
    name: '连接器插件DEMO',
    onInit: providers => {
        const { createFlowNodeDefaultConfig4Read, createFlowNodeConfigTabsMap4DataSource, createFlowNodeClass4Read } = FDL.Materials;

        const NODE_TYPE = 'DEMO_READ';

        const DATASOURCE_TYPE = 'demoDatasourceType';

        const DEFAULT_CONFIG = createFlowNodeDefaultConfig4Read({
            nodeType: NODE_TYPE,
            datasourceType: DATASOURCE_TYPE,
            defaultConfig: {
                name: 'superman',
                age: 18,
                gender: 'male',
            },
        });

        const FlowNodeConfigTabsMap = createFlowNodeConfigTabsMap4DataSource({
            render: ({ inputNodeProvider, setInputNodeProvider }) => {
                const { name, setName, age, setAge, gender, setGender } = useSpreadState(inputNodeProvider, setInputNodeProvider);

                return (
                    <div className='fdl-flex fdl-flex-col fdl-gap-[10px]'>
                        <PrepFormItem text='名称'>
                            <PrepInput
                                value={name}
                                onChange={e => {
                                    setName(e.target.value);
                                }}
                            />
                        </PrepFormItem>
                        <PrepFormItem text='年龄'>
                            <PrepInputNumber
                                value={age}
                                onChange={v => {
                                    setAge(v ?? 0);
                                }}
                            />
                        </PrepFormItem>
                        <PrepFormItem text='性别'>
                            <PrepSelect className='fdl-w-full' options={['male', 'female'].map(v => ({ value: v, label: v }))} value={gender} onChange={setGender} />
                        </PrepFormItem>
                    </div>
                );
            },
        });

        const FlowNode = createFlowNodeClass4Read(
            {
                xtype: NODE_TYPE,
                defaultConfig: DEFAULT_CONFIG,
            },
            context => {
                return {
                    xname: '自定义输入',
                    configTabs: [FlowNodeConfigTabsMap.DATA_FLOW],
                };
            },
        );

        providers.FlowNodeClassProvider.register(FlowNode);

        providers.FlowNodeConfigTabProvider.register(DATASOURCE_TYPE, {
            flowNode4Read: FlowNode,
            configTabsMap4DataSource: FlowNodeConfigTabsMap,
        });
    },
});
```

> 效果截图（数据同步中使用自定义输入、数据转换中使用自定义输入）请参考原 KMS 文档。

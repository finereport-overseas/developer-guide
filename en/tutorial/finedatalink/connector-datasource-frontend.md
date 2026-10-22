# Connector Data Source Read Plugin Interface (Frontend)

## Interface - Materials

| Interface | Description | Parameters |
| --- | --- | --- |
| `createFlowNodeDefaultConfig4Read` | Create the default config of the input operator | `{ nodeType: string; // operator type, datasourceType: string; // data source type, defaultConfig: object; // default config }` |
| `createFlowNodeConfigTabsMap4DataSource` | Create the config tabs for the data source | `{ render: ({ inputNodeProvider, setInputNodeProvider, visitor }) => ReactNode; // tab renderer }` |
| `createFlowNodeClass4Read` | Create the input operator | Same as `FDL.Materials.createFlowNodeClass` |

## Interface - Providers

| Interface | Description | Parameters |
| --- | --- | --- |
| `FlowNodeClassProvider.register` | Register the operator | `node: object; // operator` |
| `FlowNodeConfigTabProvider.register` | Register the operator config tab | `datasourceType: string; // data source type` + `{ flowNode4Read: object; // input operator for the data source, configTabsMap4DataSource: object; // data source config tab }` |

## Example

```js
FDL.PluginManager.registerPlugin({
    name: 'Connector Plugin DEMO',
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
                        <PrepFormItem text='Name'>
                            <PrepInput
                                value={name}
                                onChange={e => {
                                    setName(e.target.value);
                                }}
                            />
                        </PrepFormItem>
                        <PrepFormItem text='Age'>
                            <PrepInputNumber
                                value={age}
                                onChange={v => {
                                    setAge(v ?? 0);
                                }}
                            />
                        </PrepFormItem>
                        <PrepFormItem text='Gender'>
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
                    xname: 'Custom Input',
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

> For the effect screenshots (using the custom input in Data Sync / Data Transform), please refer to the original KMS doc.

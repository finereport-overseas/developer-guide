# 拦截修改类

FineBI 6.0 提供的前端接口都以 provider 的形式提供。拦截修改类 provider 用于自定义修改 BI 内部的加解密方法及请求钩子。

---

## `bi.provider.cipher`

自定义修改 BI 在需要使用加解密的功能场景下的加解密方法。目前作用的场景为编辑 SQL 数据集时对 SQL 语句进行的加解密，由全局变量 `BI.transmissionEncryption` 的值来决定当前是使用 AES 加密还是国密加密。

| 方法 | 签名 | 作用 |
|---|---|---|
| `encrypt` | `encrypt(fn: (pwd: string) => string)` | 自定义加密方法 |
| `decrypt` | `decrypt(fn: (pwd: string) => string)` | 自定义解密方法 |

```js
BI.config('bi.provider.cipher', (provider) => {
    provider.encrypt((str) => myEncodeFunc(str))
});
BI.config('bi.provider.cipher', (provider) => {
    provider.decrypt((str) => myDecodeFunc(str))
});
```

---

## `bi.provider.base.req`

对外提供请求前、请求成功、请求完成、请求失败的钩子。

| 方法 | 签名 | 作用 |
|---|---|---|
| `reqBefore` | `reqBefore(fn: (config: any) => newConfig)` | 请求前钩子 |
| `reqAfterSuccess` | `reqAfterSuccess(fn: (res: any) => void)` | 请求成功钩子 |
| `reqAfterComplete` | `reqAfterComplete(fn: (res: any) => void)` | 请求完成钩子 |
| `reqAfterFail` | `reqAfterFail(fn: (res: any) => void)` | 请求失败钩子 |

```js
BI.config('bi.provider.base.req', (provider) => {
    provider.reqBefore((options) => {
        console.log('请求前');
        return options;
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterSuccess((res) => {
        console.log('请求成功' + res.data);
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterComplete((res) => {
        console.log('请求完成' + res.data);
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterFail((res) => {
        console.log('请求失败' + res.errorCode);
    });
})
```

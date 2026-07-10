# Intercept and Modification Providers

All frontend interfaces provided by FineBI 6.0 are exposed in the form of providers. Intercept and modification providers are used to customize the encryption/decryption methods and request hooks inside BI.

---

## `bi.provider.cipher`

Customize the encryption/decryption methods used in scenarios where BI requires encryption or decryption. Currently this applies when editing SQL datasets — the SQL statements are encrypted/decrypted based on the value of the global variable `BI.transmissionEncryption`, which determines whether AES encryption or national cryptography (SM) is used.

| Method | Signature | Description |
|---|---|---|
| `encrypt` | `encrypt(fn: (pwd: string) => string)` | Customize the encryption method |
| `decrypt` | `decrypt(fn: (pwd: string) => string)` | Customize the decryption method |

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

Provides hooks for before-request, request-success, request-complete, and request-failure events.

| Method | Signature | Description |
|---|---|---|
| `reqBefore` | `reqBefore(fn: (config: any) => newConfig)` | Before-request hook |
| `reqAfterSuccess` | `reqAfterSuccess(fn: (res: any) => void)` | Request-success hook |
| `reqAfterComplete` | `reqAfterComplete(fn: (res: any) => void)` | Request-complete hook |
| `reqAfterFail` | `reqAfterFail(fn: (res: any) => void)` | Request-failure hook |

```js
BI.config('bi.provider.base.req', (provider) => {
    provider.reqBefore((options) => {
        console.log('before request');
        return options;
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterSuccess((res) => {
        console.log('request succeeded: ' + res.data);
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterComplete((res) => {
        console.log('request completed: ' + res.data);
    });
})
BI.config('bi.provider.base.req', (provider) => {
    provider.reqAfterFail((res) => {
        console.log('request failed: ' + res.errorCode);
    });
})
```

# 全局 service — dec.service.global

## global_service

获取方法：BI.Services.getService('dec.service.global')

**方法列表**

- [global_service~loginKick(errorCode, errorText)](#global_serviceloginkickerrorcode-errortext)
- [global_service~showErrorMasker(text)](#global_serviceshowarrormaskertext)
- [global_service~refreshToken()](#global_servicerefreshtoken)
- [global_service~isAdmin()](#global_serviceisadmin) ⇒ `*`

---

### global_service~loginKick(errorCode, errorText)

踢出登录

**Kind**: inner method of `global_service`

| Param | Description |
| --- | --- |
| errorCode | 错误码 |
| errorText | 错误信息 |

### global_service~showErrorMasker(text)

显示错误遮罩层

**Kind**: inner method of `global_service`

| Param | Description |
| --- | --- |
| text | 错误提示 |

### global_service~refreshToken()

初始化刷新 token 操作

**Kind**: inner method of `global_service`

### global_service~isAdmin() ⇒ `*`

判断当前用户是否是管理员

**Kind**: inner method of `global_service`

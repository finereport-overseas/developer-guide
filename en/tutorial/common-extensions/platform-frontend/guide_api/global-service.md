# Global Service — dec.service.global

## global_service

Access method: BI.Services.getService('dec.service.global')

**Method List**

- [global_service~loginKick(errorCode, errorText)](#global_serviceloginkickerrorcode-errortext)
- [global_service~showErrorMasker(text)](#global_serviceshowarrormaskertext)
- [global_service~refreshToken()](#global_servicerefreshtoken)
- [global_service~isAdmin()](#global_serviceisadmin) ⇒ `*`

---

### global_service~loginKick(errorCode, errorText)

Force log out the current user.

**Kind**: inner method of `global_service`

| Param | Description |
| --- | --- |
| errorCode | Error code |
| errorText | Error message |

### global_service~showErrorMasker(text)

Display an error overlay mask.

**Kind**: inner method of `global_service`

| Param | Description |
| --- | --- |
| text | Error prompt text |

### global_service~refreshToken()

Initialize the token refresh operation.

**Kind**: inner method of `global_service`

### global_service~isAdmin() ⇒ `*`

Check whether the current user is an administrator.

**Kind**: inner method of `global_service`

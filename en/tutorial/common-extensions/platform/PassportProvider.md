# PassportProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.PassportProvider` |

## Background and Use Cases

The FineReport decision platform provides three default authentication methods: built-in platform authentication, LDAP authentication, and HTTP authentication. The `PassportProvider` interface allows developers to extend fully custom authentication methods to meet specific business requirements.

## Interface Definition

`PassportProvider` defines three core methods:

- `passportType()`: Returns the authentication type identifier
- `classForPassportBean()`: Registers the business object class
- `classForPassportConfig()`: Registers the database configuration class

The `Passport` interface handles authentication logic, verifying username, input password, saved password, and hash password through the `checkTicket()` method.

`PassportBean` is an abstract class for managing authentication configuration. It contains type and user creation source properties, along with abstract methods `markType()`, `createPassportBean()`, and `createPassport()`.

## Supported Versions

| Product Line | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 10.0 | Supported | — |
| FR | 11.0 | Supported | |
| BI | 5.1 | Supported | Dashboard not supported |
| BI | 5.1.2 | Supported | Dashboard not supported |
| BI | 5.1.3 | Supported | Dashboard not supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <WebResourceProvider class="your classname"/>
    <PassportProvider class="your classname"/>
</extra-decision>
```

## How It Works

Plugins are loaded via `ExtraDecisionClassManager.getInstance().getArray()`. After frontend configuration is persisted to the backend, the system first matches built-in types, then matches extension types declared by plugins.

## Notes

- The return values of `passportType()` and `markType()` must exactly match the frontend configuration identifiers
- This interface handles authentication only; it does not involve single sign-on (SSO)
- After implementing this interface, user synchronization and password management features will become unavailable

## Useful Links

- Demo: [demo-passport-provider](https://code.fanruan.com/hugh/demo-passport-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [demo-custom-passport](https://code.fanruan.com/fanruan/demo-custom-passport)
- [demo-ldaps-passport](https://code.fanruan.com/fanruan/demo-ldaps-passport)

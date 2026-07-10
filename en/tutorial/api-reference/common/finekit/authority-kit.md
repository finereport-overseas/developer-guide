# AuthorityKit — Permission Utility

## Source

[AuthorityKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/decision/AuthorityKit.java)

## Description

AuthorityKit is primarily used to **retrieve module IDs**. The permission-setting APIs it provides are not commonly needed and cannot be guaranteed to remain stable. As a result, it is generally recommended to use this kit only to obtain module IDs, and to avoid calling other APIs to control permissions directly — doing so may cause instability when upgrading to future product versions.

### `AuthorityKit.Management.$KEY` Reference Table

| Key | Corresponding Decision Platform Permission Module |
| --- | --- |
| `USER` | User Management |
| `AUTHORITY` | Permission Management |
| `DIRECTORY` | Directory Management |
| `APPEARANCE` | Appearance Configuration |
| `CONNECTION` | Data Connection |
| `SYSTEM` | System Management |
| `SCHEDULE` | Scheduled Tasks |
| `MOBILE` | Mobile Platform |
| `REGISTER` | Registration Management |
| `PLUGIN` | Plugin Management |
| `SECURITY` | Security Management |
| `TEMPLATE` | Template Certification |
| `MAP` | Map Configuration |
| `MAINTENANCE` | Smart Operations |

> Note: In related plugins, management system modules extended by other plugins can still be matched using these key values.

## Related Links

- [finekit repository](https://code.fanruan.com/fanruan/finekit)

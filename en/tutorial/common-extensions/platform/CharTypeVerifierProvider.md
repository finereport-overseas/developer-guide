# CharTypeVerifierProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-decision |
| Full Class Name | `com.fr.decision.fun.CharTypeVerifierProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.decision.fun.CharTypeVerifierProvider) |

## Overview

The platform applies regex-based validation to user mobile numbers and email addresses on the backend. This interface allows you to override the validation logic to extend the rules for mobile numbers or email addresses.

Currently, only the user's mobile number and email (corresponding fields in `CharLimitType`) are supported. Other `CharLimitType` fields require adaptation in future major versions.

## Interface Definition

```java
public interface CharTypeVerifierProvider extends PlatformScaffoldVerifierProvider, Mutable {
    String MARK_STRING = "CharTypeVerifierProvider";

    int CURRENT_LEVEL = 1;
}

public interface PlatformScaffoldVerifierProvider {

    /**
     * Validate a string
     *
     * @param targetStr     the original string
     * @param charLimitType the validation type
     * @return whether the string contains illegal characters (true means illegal characters found)
     */
    boolean containIllegalChars(String targetStr, CharLimitType charLimitType);

    /**
     * Selector — determines whether this verifier handles the current validation
     *
     * @param targetStr     the original string
     * @param charLimitType the validation type
     * @return whether this verifier is selected
     */
    boolean accept(String targetStr, CharLimitType charLimitType);
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-decision` tag:

```xml
<extra-decision>
    <CharTypeVerifierProvider class="com.fr.plugin.MyCharTypeVerifier"/>
</extra-decision>
```

## Example Code

The following example extends mobile number validation to support both mainland China and Hong Kong formats:

```java
public class MyCharTypeVerifier implements CharTypeVerifierProvider {

    @Override
    public boolean accept(String targetStr, CharLimitType charLimitType) {
        // Only handle mobile number validation
        return charLimitType == CharLimitType.MOBILE;
    }

    @Override
    public boolean containIllegalChars(String targetStr, CharLimitType charLimitType) {
        // Custom validation: supports mainland China numbers (11 digits starting with 1) or Hong Kong numbers (starting with +852)
        if (targetStr == null) {
            return true;
        }
        boolean isMainland = targetStr.matches("^1[3-9]\\d{9}$");
        boolean isHongKong = targetStr.matches("^\\+852[0-9]{8}$");
        // Return true if invalid
        return !isMainland && !isHongKong;
    }
}
```

## Notes

- The verifier only takes effect when `accept()` returns `true`. Use this method to restrict which validation types the verifier handles.
- Only `CharLimitType.MOBILE` (mobile number) and `CharLimitType.EMAIL` (email) have been verified. Other types require future major version adaptation.
- When multiple verifiers are registered, the platform calls `accept()` on each in registration order. The first matching verifier takes effect.

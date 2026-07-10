# Output Standards - Java

When sending data from the backend to the frontend, always use the standard methods provided by `WebUtils`. Writing custom-formatted strings directly to `PrintWriter` is not allowed.

---

## Success Response

```java
WebUtils.flushSuccessMessage(
    HttpServletRequest req,
    HttpServletResponse res,
    PrintWriter writer,
    JSONObject data
) throws Exception
```

Use this to send a standard success JSON response to the frontend.

---

## Failure Response

```java
WebUtils.flushFailureMessageAutoClose(
    HttpServletRequest req,
    HttpServletResponse res,
    int errorCode,
    String description
) throws Exception
```

Use this to send a standard failure JSON response containing an error code and description to the frontend.

---

## Notes

- All scenarios that need to send data to the client **must** use one of the methods above.
- Any user-visible text in `description` must be internationalized using `Inter.getLocText()`.
- Error codes must use predefined constants from the project — magic numbers are not allowed.

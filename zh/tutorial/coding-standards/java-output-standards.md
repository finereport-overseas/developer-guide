# 输出规范 - Java

后端向前端输出数据时，必须使用 `WebUtils` 提供的标准方法，禁止直接向 `PrintWriter` 写入自定义格式的字符串。

---

## 成功响应

```java
WebUtils.flushSuccessMessage(
    HttpServletRequest req,
    HttpServletResponse res,
    PrintWriter writer,
    JSONObject data
) throws Exception
```

用于向前端输出标准的成功 JSON 响应。

---

## 失败响应

```java
WebUtils.flushFailureMessageAutoClose(
    HttpServletRequest req,
    HttpServletResponse res,
    int errorCode,
    String description
) throws Exception
```

用于向前端输出包含错误码和描述文案的标准失败 JSON 响应。

---

## 注意事项

- 所有需要向客户端输出数据的场景，**必须**调用上述方法之一
- `description` 中出现的用户可见文案需做国际化处理（使用 `Inter.getLocText()`）
- 错误码应使用项目中已定义的常量，不得使用魔术数

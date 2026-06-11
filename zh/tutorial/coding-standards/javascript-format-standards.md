# 格式规范（JavaScript）

---

## 1. 关键字、括号、操作符之间加空格

`if` 与 `(` 之间、操作符两侧、`||` 两侧均需有空格：

```js
// 正确
if (xmlFile == null || !xmlFile.exists()) {
    return;
}
```

---

## 2. 大括号格式

起始括号与代码同行，结束括号单独一行：

```js
$.extend(Array.prototype, {
    /**
     * 检查指定的值是否在数组中
     * @param {Object} o 要检查的值
     * @return {Number} o 在数组中的索引（不在则返回 -1）
     */
    indexOf: function(o) {
        for (var i = 0, len = this.length; i < len; i++) {
            if (FR.equals(o, this[i])) {
                return i;
            }
        }
        return -1;
    }
});
```

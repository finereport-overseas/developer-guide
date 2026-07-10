# Format Standards (JavaScript)

---

## 1. Add Spaces Around Keywords, Parentheses, and Operators

Add a space between `if` and `(`, on both sides of operators, and around `||`:

```js
// Correct
if (xmlFile == null || !xmlFile.exists()) {
    return;
}
```

---

## 2. Brace Style

Opening brace on the same line as the code, closing brace on its own line:

```js
$.extend(Array.prototype, {
    /**
     * Check whether the specified value exists in the array.
     * @param {Object} o The value to check.
     * @return {Number} The index of o in the array, or -1 if not found.
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

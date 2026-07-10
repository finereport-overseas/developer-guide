# JavaScript Code Standards

This section defines the JavaScript coding standards for FanRuan plugin development. All plugins involving frontend development must comply with these rules.

---

## 1. Semicolons at End of Statements

**Always** add a semicolon at the end of every statement, except for `for`, `function`, `if`, `switch`, `try`, and `while` blocks.

Missing semicolons can cause parsing ambiguity:

```js
// Wrong
MyClass.prototype.myMethod = function() {
    return 42;
}  // missing semicolon here

(function() {
    // initialization logic
})();
// The two blocks above will be interpreted as: myMethod is called, returns 42,
// which is then called as a function — resulting in an error
```

---

## 2. Avoid Trailing Commas

```js
// Wrong
var arr = [1, 2, 3,];  // trailing comma
FR.Ajax({
    url: 'http://someurl',
    data: {'paraA': 'aa', 'paraB': 'bb'},  // trailing comma — may cause errors in IE
});

// Correct
var arr = [1, 2, 3];
var ajaxConfig = {
    url: 'http://someurl',
    data: {'paraA': 'aa', 'paraB': 'bb'}
};
```

---

## 3. Always Use Braces for Loop and Conditional Bodies

```js
// Not recommended
if (condition) statement;

// Correct
if (condition) {
    statement;
}
```

---

## 4. Do Not Use with, void, or eval

- `eval()` executes arbitrary string code, which is extremely dangerous when user input is involved.
- `with` makes scope semantics unclear and can conflict with local variables.

---

## 5. Avoid Using new to Construct Primitive Types

```js
// Not recommended
var obj = new Object();
var arr = new Array();

// Correct
var obj = {};
var arr = [];
```

---

## 6. Use for-in Only for Objects, Not Arrays

`for-in` iterates over all keys in an object including its prototype chain, making behavior unpredictable on arrays:

```js
// Wrong
var arr = ['aa', 'bb', 'cc', 'dd'];
for (var el in arr) {
    // ...
}

// Correct
var arr = ['aa', 'bb', 'cc', 'dd'];
for (var i = 0, len = arr.length; i < len; i++) {
    var el = arr[i];
    // ...
}
```

---

## 7. Always Declare Variables with var

Variables declared without `var` leak into the global scope:

```js
// Wrong
FS = {};
FR = {};
contentPane = {};

// Correct
window.FS = {};
window.FR = {};
window.contentPane = {};
```

---

## 8. Use FR.ajax() Instead of $.ajax()

`FR.ajax()` automatically encodes `data`, preventing parameter encoding errors:

```js
// Not recommended
$.ajax({
    url: 'http://www.baidu.com',
    data: {'keywords': 'Chinese text'},
    success: function() {}
});

// Correct
FR.ajax({
    url: 'http://www.baidu.com',
    data: {'keywords': 'Chinese text'},
    success: function() {}
});
```

Do not use `FR.get()`, `FR.post()`, `$.get()`, or `$.post()`.

---

## 9. Prefix Private Methods with an Underscore

```js
// Not recommended
FR.Button = FR.extend(FR.Widget, {
    defaultConfig: function() {
        return {cls: 'fr-btn'};
    }
});

// Correct
FR.Button = FR.extend(FR.Widget, {
    _defaultConfig: function() {
        return {cls: 'fr-btn'};
    }
});
```

---

## 10. Date Constructor

`new Date("2014-01-01")` has browser compatibility issues and is not supported in older versions of IE:

```js
// Wrong
var date = new Date("2014-01-01");

// Correct
var date = new Date(2014, 0, 1);  // Note: months are zero-indexed
```

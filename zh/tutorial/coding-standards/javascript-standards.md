# JavaScript 代码规范

本章节规定了帆软插件开发中 JavaScript 代码的编写规范，所有涉及前端开发的插件均应遵守。

---

## 1. 语句结尾分号

**必须**在每条语句末尾添加分号，`for`、`function`、`if`、`switch`、`try`、`while` 语句块除外。

缺少分号可能导致解析歧义：

```js
// 错误示例
MyClass.prototype.myMethod = function() {
    return 42;
}  // 此处缺少分号

(function() {
    // 初始化逻辑
})();
// 上面两段会被解释为：myMethod 被调用后返回 42，再次被匿名函数调用，导致错误
```

---

## 2. 避免多余的逗号

```js
// 错误
var arr = [1, 2, 3,];  // 末尾多余逗号
FR.Ajax({
    url: 'http://someurl',
    data: {'paraA': 'aa', 'paraB': 'bb'},  // 末尾多余逗号，IE 下可能报错
});

// 正确
var arr = [1, 2, 3];
var ajaxConfig = {
    url: 'http://someurl',
    data: {'paraA': 'aa', 'paraB': 'bb'}
};
```

---

## 3. 循环和判断体必须使用大括号

```js
// 不推荐
if (condition) statement;

// 正确
if (condition) {
    statement;
}
```

---

## 4. 禁止使用 with、void、eval

- `eval()` 会执行任意字符串代码，包含用户输入时极为危险
- `with` 使作用域语义不清晰，容易与局部变量冲突

---

## 5. 避免使用 new 构造基础类型

```js
// 不推荐
var obj = new Object();
var arr = new Array();

// 正确
var obj = {};
var arr = [];
```

---

## 6. for-in 只用于对象，不用于数组

`for-in` 遍历对象及其原型链上的所有键，用于数组时行为不可预期：

```js
// 错误
var arr = ['aa', 'bb', 'cc', 'dd'];
for (var el in arr) {
    // ...
}

// 正确
var arr = ['aa', 'bb', 'cc', 'dd'];
for (var i = 0, len = arr.length; i < len; i++) {
    var el = arr[i];
    // ...
}
```

---

## 7. 声明变量必须使用 var

未加 `var` 的变量会泄漏到全局作用域：

```js
// 错误
FS = {};
FR = {};
contentPane = {};

// 正确
window.FS = {};
window.FR = {};
window.contentPane = {};
```

---

## 8. 使用 FR.ajax() 替代 $.ajax()

`FR.ajax()` 会对 `data` 自动编码，避免参数编码错误：

```js
// 不推荐
$.ajax({
    url: 'http://www.baidu.com',
    data: {'keywords': '中文'},
    success: function() {}
});

// 正确
FR.ajax({
    url: 'http://www.baidu.com',
    data: {'keywords': '中文'},
    success: function() {}
});
```

禁止使用 `FR.get()`、`FR.post()`、`$.get()`、`$.post()`。

---

## 9. 私有方法以下划线开头

```js
// 不推荐
FR.Button = FR.extend(FR.Widget, {
    defaultConfig: function() {
        return {cls: 'fr-btn'};
    }
});

// 正确
FR.Button = FR.extend(FR.Widget, {
    _defaultConfig: function() {
        return {cls: 'fr-btn'};
    }
});
```

---

## 10. 日期构造函数

`new Date("2014-01-01")` 存在浏览器兼容问题，低版本 IE 不支持：

```js
// 错误
var date = new Date("2014-01-01");

// 正确
var date = new Date(2014, 0, 1);  // 注意：月份从 0 开始
```

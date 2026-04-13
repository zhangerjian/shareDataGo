---
name: async-skill
description: 封装 Vue 组件中的异步方法调用，提供标准的 async/await + try/catch 结构模板，包含 loading 状态控制、接口响应判断、竞态序号控制等规范。当用户需要新增异步方法、封装接口调用、或处理请求竞态时自动应用此技能。
---

# 异步方法调用封装规范

## 标准模板

```js
async excuteFun() {
  try {
    let params = {
      // 请求参数
    };

    let res = await excuteApi(params);

    if (res && res.code == 200) {
      // 成功处理逻辑
    }
  } catch (e) {
    console.log(e);
  }
}
```

## 带 loading 状态的模板

```js
async excuteFun() {
  this.loading = true;
  try {
    let params = {
      // 请求参数
    };

    let res = await excuteApi(params);

    if (res && res.code == 200) {
      // 成功处理逻辑
    }
  } catch (e) {
    console.log(e);
  } finally {
    this.loading = false;
  }
}
```

## 带竞态控制的模板（多请求场景）

适用于 Tab 切换、筛选条件频繁变化等可能发生竞态的场景。

**初始化（在 `created` 钩子中）：**

```js
created() {
  this.requestSeq = 0; // 非响应式实例属性，不放在 data() 中
}
```

**方法体：**

```js
async excuteFun() {
  const seq = ++this.requestSeq;
  this.loading = true;
  try {
    let params = {
      // 请求参数
    };

    let res = await excuteApi(params);

    if (seq !== this.requestSeq) return; // 过期请求，丢弃

    if (res && res.code == 200) {
      // 成功处理逻辑
    }
  } catch (e) {
    if (seq !== this.requestSeq) return; // catch 中同样需要判断
    console.log(e);
  } finally {
    if (seq === this.requestSeq) {
      this.loading = false;
    }
  }
}
```

## 注意事项

1. **竞态序号命名**：在 Vue 2 中，`requestSeq` 必须在 `created()` 中作为普通实例属性初始化（`this.requestSeq = 0`），**不要放在 `data()` 中**，也**不要用下划线开头**（如 `_requestSeq`），否则 Vue 2 响应式系统会导致自增不稳定。

2. **loading 控制**：所有分支（成功、失败、异常）都要确保 `loading` 状态被正确关闭，推荐使用 `finally`；有竞态控制时需在 `finally` 中加 `seq` 判断。

3. **响应判断**：统一使用 `res && res.code == 200` 判断接口成功。

4. **参数命名**：方法名、API 函数名根据业务替换，`params` 对象按需填写。

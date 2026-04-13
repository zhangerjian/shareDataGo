---
name: vue-component-template
description: 为 Vue 2 项目生成标准的单文件组件（SFC）初始化模板，包含 template、script、style 三段式结构。当用户需要新建 .vue 文件、初始化 Vue 组件骨架，或询问组件基础结构时自动应用此技能。
---

# Vue 组件初始化模板

## 标准模板

每次新建 `.vue` 文件时，使用以下结构作为起点：

```vue
<template>
  <div></div>
</template>

<script>
export default {
  data() {
    return {};
  }
};
</script>

<style lang="less" scoped></style>
```

## 使用说明

1. `<template>` 根元素使用 `<div>`，视需要添加 class 名称
2. `<script>` 中 `export default {}` 按需补充 `name`、`props`、`components`、`computed`、`methods` 及生命周期钩子等选项
3. `<style>` 默认使用 `lang="less"` + `scoped`，若组件需要全局样式则去掉 `scoped`

## 常见扩展结构

需要 props、方法和生命周期钩子时：

```vue
<template>
  <div class="component-name"></div>
</template>

<script>
export default {
  name: 'ComponentName',
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  data() {
    return {};
  },
  created() {},
  mounted() {},
  beforeDestroy() {},
  methods: {}
};
</script>

<style lang="less" scoped></style>
```

## 钩子函数说明

Vue 2 常用生命周期钩子及使用时机：

| 钩子 | 时机 | 常见用途 |
|------|------|---------|
| `created` | 实例创建完成，DOM 未挂载 | 发起初始化接口请求、初始化数据 |
| `mounted` | DOM 挂载完成 | 操作 DOM、初始化第三方库、需要 `$refs` 的逻辑 |
| `beforeDestroy` | 实例销毁前 | 清除定时器、取消事件监听、释放资源 |
| `updated` | 数据变更导致 DOM 重渲染后 | 需要在数据变化后操作 DOM（慎用，优先用 watch） |
| `watch` | 监听数据变化 | 响应 props 或 data 变化，执行异步操作 |

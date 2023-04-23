# xx-date-time-picker
for uniapp dateTimePicker plugin,require vue3

## 使用

```html
<script setup>
  const pickerRef = ref(null)
  function choose () {
    // 初始时间
    pickerRef.value.open(new Date())
  }
  function onConfirm(time) {
    // do something
  }
</script>
<template>
  <xx-date-time-picker ref="pickerRef" @confirm="onConfirm"></xx-date-time-picker>
</template>

```
## 文档说明
### 依赖说明
-需要安装 `sass` 插件
### 属性说明
| 参数 |  类型  | 默认值 | 说明 |
|------|--------|-------|------|
|years| Array | [2000, new Date().getFullYear()] | 用于生成年范围|
|mode| String 'light' \| dark | light| 主题模式|
### 事件说明
|名称|说明|
|----|----|
|confirm| 返回当选中的日期|
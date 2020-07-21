---
title: Vue
date: 2020-07-21 08:41:00
categories:
    - 框架
tags:
    - Vue
---

# vue样式中的 scoped 和 css module对比

scoped 编译样式的时候会添加一个class[data-v-id]值来做唯一标识，模板和样式中的class对应上即可，可以层层嵌套直接取样式名使用，更为便捷

module 编译样式的时候class会生成对应的模块名，模板中需要根据从style里边定义的class中读取，只能获取定义在外部的class名

```vue
<template>
	<button class="button">button</button>
</template>
<style scoped>
    .button {
        color: red;
    }
</style>
<!-- 借助postcss，转换成一下内容 -->
<style>
    .button[data-v-f61kqi1] {
        color: red;
    }
</style>
<button class="button" data-v-f61kqi1>button</button>
```

```vue
<template>
	<button :class="`${style.button}`">button</button>
</template>
<style module>
    .button {
        color: red;
    }
</style>

<style>
    .ComponentName__button__2kxt {
        color: red;
    }
</style>
<button class="ComponentName__button__2kxt">button</button>
```


---
title: Vue 正式学习笔记
date: 2023-08-22 15:35:04
updated: 2023-08-22 15:35:04
categories: Vue
tags: [JS, JavaScript, 前端, Vue, Element-UI, UI]
description: 
thumbnail: 
banner_img: 
---

# Vue 正式学习笔记

经过了前面 Javascript 和 Nodejs 前置知识的铺垫，我想我应该可以正式学习 Vue 这个前端框架了叭。

先看视频放松下过一遍：  
[【2023最新版】Vue3从入门到精通，零基础小白也能听得懂，写得出，web前端快速入门教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Rs4y127j8/?)

reference：[创建一个 Vue 应用 | Vue.js](https://cn.vuejs.org/guide/essentials/application.html)

## Fast Start

Install vue framework in NodeJS

```shell
npm install vue@latest -g # 全局安装

npm create vue@latest # 创建 vue3 项目

cd <your-project-name>
npm install
npm run dev

npm run build # 发布到生产环境 ./dist
```

## 响应式 API 和组合式 API

HelloWorld.vue

```html
<template>
  <h1>{{ msg }} Start Learning Vue Framework</h1>
  <button @click="switchName">switchName</button>
</template>

<script setup>
  // 响应式 API 写法
  import { ref } from 'vue'
  const msg = ref("WhaleFall");

  // switch name
  function switchName() {
      msg.value = msg.value == "WhaleFall" ? "颖怡" : "WhaleFall";
  }
</script>

<script>
  // 组合式 API 写法
  export default {
      data: function () {
          return {
              msg: "Mess"
          }
      },
      methods: {
          switchName() {
              this.msg = this.msg == "WhaleFall" ? "颖怡" : "WhaleFall";
          }
      },

  }
</script>

```

App.vue

```html
<template>
    <HelloWorld />
</template>
<script setup>
import HelloWorld from './components/HelloWorld.vue';
</script>
<style scoped></style>
```

main.js

```js
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App);
app.mount('#app');
```

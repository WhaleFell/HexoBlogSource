---
title: Vue3 正式学习笔记
date: 2023-08-22 15:35:04
updated: 2023-08-22 15:35:04
categories: Vue
tags: [JS, JavaScript, 前端, Vue, Element-UI, UI]
description: 
thumbnail: 
banner_img: 
---

# Vue3 正式学习笔记

经过了前面 Javascript 和 Nodejs 前置知识的铺垫，我想我应该可以正式学习 Vue 这个前端框架了叭。

先看视频放松下过一遍：  

[【2023最新版】Vue3从入门到精通，零基础小白也能听得懂，写得出，web前端快速入门教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Rs4y127j8/?)

> 上面的视频虽然说得是 Vue3 但是是按照 **组合式 API** 讲解的，Vue3 文档推荐使用 **响应式 API** 编写代码。不过具体原理大同小异。

reference：[创建一个 Vue 应用 | Vue.js](https://cn.vuejs.org/guide/essentials/application.html)

需要用到的组件库、UI、AJAX 请求库 文档。

1. Element-UI 组件库
2. axios  `/ˈæksɪos/` AJAX 请求库
3. Vue Route 路由

粗略的过了一遍上述文档就可以看这个 《Vue3 入门指南与实战案例》 ：[出版说明 | Vue3 入门指南与实战案例](https://vue3.chengpeiquan.com/)

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

## 声明响应式状态

在组合式 API 中，推荐使用 [`ref()`](https://cn.vuejs.org/api/reactivity-core.html#ref) 函数来声明响应式状态，定义响应式依赖。

```javascript
import { ref } from 'vue'
const count = ref(0) // 初始化数值
```

## 计算属性 computed

返回值为一个**计算属性 ref**，通过 `publishedBooksMessage.value` 访问计算结果，计算属性 ref 也会在模板中自动解包，因此在模板表达式中引用时无需添加 `.value`。

**计算属性值会基于其响应式依赖被缓存**

```javascript
const publishedBooksMessage = computed(
// 依赖于 author 对象
// 只要 `author.books` 不改变，无论多少次访问 `publishedBooksMessage` 都会立即返回先前的计算结果，而不用重复执行 getter 函数。
() => { return author.books.length > 0 ? 'Yes' : 'No' }
)
```

计算属性默认是只读的。当你尝试修改一个计算属性时，你会收到一个运行时警告。只在某些特殊场景中你可能才需要用到“可写”的属性，你可以通过同时提供 getter 和 setter 来创建。

```javascript
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  // 应只做计算而没有任何其他的副作用
  // **不要在 getter 中做异步请求或者更改 DOM**！
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

## Watch 监听器

每次响应式状态变化时触发回调函数：

```javascript
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')

// 可以直接侦听一个 ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Thinking…'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```

## 使用 Vite 脚手架 创建项目

```shell
npm create vite
cd myself-vite
pnpm install
pnpm run dev

# install element ui
[快速开始 | Element Plus](https://element-plus.org/zh-CN/guide/quickstart.html#%E5%AE%8C%E6%95%B4%E5%BC%95%E5%85%A5)

pnpm install -D unplugin-vue-components unplugin-auto-import
pnpm install -D @types/node
pnpm install element-plus

# icons
pnpm install @element-plus/icons-vue

# route
pnpm install vue-route

```

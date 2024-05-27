---
title: Vue3 + vite + Ts + pinia 全家桶学习
date: 2024-01-13 15:21:31
updated: 2024-01-13 15:21:31
categories:
  - Vue
tags:
  - Vue3
  - JavaScript
  - JS
  - Vue
  - 前端
description:
thumbnail:
banner_img:
---

# Vue3 + vite + Ts + pinia 全家桶学习

## Reference

1. BiliBili 小满 ZS VUE3 教程：[Vue3 + vite + Ts + pinia + 实战 + 源码 + electron](https://www.bilibili.com/video/BV1dS4y1y7vd/?vd_source=8b588c19ea4cd636da362bae86b1564a)

## 概述

1. MMVVM 架构
2. Options API and Composition API
3. Vue3 的优化：重写双向数据绑定，基于 ES6 Proxy

## Computed attribute 计算属性

```javascript
// computed-attribute.vue
import { ref, computed } from "vue";
let firstName = ref("John");
let lastName = ref("Doe");

// 1. optional computed
let name =
  computed <
  string >
  {
    get: () => `${firstName.value}-${lastName.value}`,
    set: (newValue) => {
      [firstName.value, lastName.value] = newValue.split("-");
    },
  };

// 2. function computed
// 只支持一个 getter 函数，不允许修改值 readonly
let name2 = computed(() => `${firstName.value}-${lastName.value}`);
```

## Watch listener

```javascript
// vue3 watch listener
import { ref, watch, watchEffect } from "vue";


let message = ref<string>("hello world");
let message2 = ref<string>("hello world2");
let message3 = ref<object>({ "foo": { "bar": "hello world3" } });


watch(message, (newValue, oldValue) => {
    console.log("newValue", newValue);
    console.log("oldValue", oldValue);
})

// 监听多个数据源
watch([message, message2], ([newMessage, newMessage2], [oldMessage, oldMessage2]) => {
    console.log("newMessage", newMessage);
    console.log("newMessage2", newMessage2);
    console.log("oldMessage", oldMessage);
    console.log("oldMessage2", oldMessage2);
})

// 监听一个对象，开启深度监听
// 引用类型返回的 newValue 和 oldValue 是一样的
// 如果用 reactive 不用开启深度监听
// ref: 监听属性的单一值需要用一个 getter 函数
watch(message3, (newValue, oldValue) => {
    console.log("newValue", newValue);
    console.log("oldValue", oldValue);
}, {
    deep: true, // 开启深度监听
    immediate: true, // 立即执行 watch callback 一次
    flush: "pre" // pre: 组件更新前执行，post: 组件更新后执行 sync: 同步执行
})

// watchEffect 高级监听器
// oninvaildate 消除副作用的函数(回调)
const stop = watchEffect((oninvaildate) => {
    // 先执行 oninvaildate 回调执行其他代码 比如防抖
    oninvaildate(() => {
        console.log("oninvaildate");
    })
    // console.log("message", message.value);
    // console.log("message2", message2.value);
    // console.log("message3", message3.value);
    // get dom element
    const ipt: HTMLInputElement = document.querySelector("#input") as HTMLInputElement;
    console.log('ipt:', ipt);
}, {
    flush: "post", // before dom loaded
    // debug
    onTrigger(e) {
        debugger;
        console.log("onTrigger", e);
    },
})

// 调用 watchEffect 返回的 stop 函数可以停止监听
const stopWatch = () => {
    stop();
}

```

## Vue lifespan 生命周期

onBeforeMount(挂载前) -> onMounted(挂载后) -> onBeforeUpdate(更新数据之前) -> onUpdated(更新数据之后) -> onBeforeUnmount(卸载之前) -> onUmounted(卸载之后)

在 setup 语法糖模式中没有 beforeCreate 和 created 两个生命周期，用 setup(callback) 来代替。

调试用的两个 hook，`onRenderTracked`，`onRenderTriggered`，应该是依赖 effect 依赖收集之类的钩子，具体可以看 Vue3 的响应式 ref reactive 原理。

## BEM 架构和 layout 布局和 sass CSS 预处理器

BEM 架构： block (块)，element (元素)，modify (修饰)，指的是 namespace 的命名风格，在 element-UI 中，`el-button__wrapper` 表示一个 block 中的 element。`el-button--primary` 表示 button 的自定义按钮风格 modify。

```sass
$namespace: 'xm' !default;
$block-select: "0" !default;
$elem-sel: "__" !default;
$modify-sel: "--" !default;
```

Sass.hk 中文网： [Sass教程 Sass中文文档 | Sass中文网](https://sass.hk/docs/)

> Sass 是一款强化 CSS 的辅助工具，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能.

## 父子组件传参

子组件 children

```javascript
// defineProps 利用 ts 泛型
const props = defineProps<{
    msg: string
}>()

// 使用 ts 泛型时定义 default
withDefaults(defineProps<{
    msg: string
}>(), {
    msg: "hello world",
    array: () => [1, 2, 3]
}

// defineProps 传入一个对象
const props = defineProps({
    msg: {
        type: String,
        required: true,
        default: "hello world"
    }
})
console.log("props:", props.msg);

// 给父组件传值使用 emit 自定义事件
// define emit 不使用 ts 泛型,直接传入事件名
const emit = defineEmits(["changeMsg"])

// define emit 使用 ts 泛型
const emit = defineEmits<{
    (event: "changeMsg", msg: string): boolean
}>()


const changeMsg = () => {
    // emit 事件
    emit("changeMsg", "hello world2")
}

// 向父组件暴露方法和属性,用 ref 接收
defineExpose({
    msg: props.msg,
    changeMsg
})

```

父组件：

```javascript
import { ref } from 'vue';
import Child from './child-component.vue'

// 1. Define the type of the child component
// 定义一个子组件的类型,通过 component 的 ref 获取
const childVue = ref<InstanceType<typeof Child>>()
// 2. and then it can use the child component's methods and properties which are exposed by defineExpose
// defineExpose 向父组件暴露方法和属性
// 然后就可以使用子组件暴露出来的方法和属性了
console.log(childVue.value?.msg);

const changeMsg = (newMsg: string) => {
    childVue.value?.changeMsg()
    console.log('newMsg:', newMsg);
}
```

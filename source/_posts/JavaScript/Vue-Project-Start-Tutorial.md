---
title: Vue3 项目脚手架搭建和运行指北
date: 2023-10-11 16:19:66
updated: 2023-10-11 16:19:66
categories:
  - Vue
tags:
  - 前端
  - Vue
  - Vue3
  - JavaScript
  - JS
description: 
thumbnail: 
banner_img:
---

# Vue3 项目脚手架搭建和运行指北

本指南带你从 0 到 有 的编写一个 Vue3 项目的脚手架，解剖完整的 Vue3 项目结构和各种配置文件，并手把手教你配置 VSCode 达到最高效率，运用到以下技术：

- NodeJS
- TypeScript -- JS 的超集，添加友好的类型提示和更多功能
- Vscode -- 地表最强写 Vue3 IDE
- pnpm -- 比 npm 更好的包管理工具
- [Vite `/vit/`](https://cn.vitejs.dev/guide/) -- Vue3 构建、打包工具，比 webpack 更好
- Axios -- Promise 异步请求库
- Pinia -- vue3 全局状态管理
- ElementUI -- Vue3 UI 组件库
- Vueuse [GitHub - vueuse/vueuse: Collection of essential Vue Composition Utilities for Vue 2 and 3](https://github.com/vueuse/vueuse) -- Vue3 composition API utils Vue3 组合式 API 工具集
- Babel -- 编译适应多个平台的 JS [Babel 的使用](https://vue3.chengpeiquan.com/guide.html#babel-%E7%9A%84%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE)

## Install NodeJS、TypeScript、pnpm、vite

安装 NodeJS(默认)，安装 TypeScript，换成 pnpm 包管理工具，安装 Vite 打包工具。

```shell
# 将 ts 添加到 dev dependencies
npm install -D typescript ts-node

# 全局安装 pnpm 包管理工具
npm install -g pnpm
```

## Vscode IDE

安装以下 extension：

1. `Vue Language Features (Volar)` Volar -- Vue3 支持
2.  `TypeScript Vue Plugin (Volar)` Volar -- TS 支持
3. Auto Close Tag -- 标签自动合并
4. Auto Rename Tag -- 标签自动修改
5. EditorConfig for VSCode -- editorconfig code rule
6. Prettier for VSCode -- prettier `.prettierrc` code rule
7. ESLint for VSCode -- ESLint VSC 支持，需要安装 ESLint package

## Create Vite Framework

选择 ts vue3 技术栈

```shell
pnpm create vite
pnpm init vite@lastest

pnpm install
pnpm install -D typescript ts-node
```

## Set vite.config.ts/tsconfig.json

### Path alias 路径别名

vite.config.ts：

reference: [javascript - \`Vue3 - Vite\` project alias src to @ not working - Stack Overflow](https://stackoverflow.com/questions/66043612/vue3-vite-project-alias-src-to-not-working)

```js
import path from "path";
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
const pathSrc = path.resolve(__dirname, "src");

export default defineConfig({
  // …
  resolve: {
    alias: {
      '@': pathSrc,// 源码根目录
    },
  },
  // …
})
```

### tsconfig.json TS 配置

```json
{
  "compilerOptions": {
    // …
 // path alias
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"],
    }
    // …
  },
  // …
}
```

## Code rule 代码规范

### .editorconfig

`.editorconfig`：这个文件的作用是强制编辑器以该配置来进行编码，比如缩进统一为空格而不是 Tab ，每次缩进都是 2 个空格而不是 4 个等等。

VSCode 需要安装 [EditorConfig for VSCode 扩展](https://vue3.chengpeiquan.com/upgrade.html#editorconfig-for-vs-code) 。

```ini
# http://editorconfig.org
root = true

[*]
charset = utf-8
end_of_line = lf
indent_size = 2
indent_style = space
insert_final_newline = true
max_line_length = 80
trim_trailing_whitespace = true

[*.md]
max_line_length = 0
trim_trailing_whitespace = false
```

### Prettier [英 /'prɪtɪ/ 漂亮]

`.prettierrc`

配合 VSCode 的 [VSCode Prettier](https://vue3.chengpeiquan.com/upgrade.html#vscode-prettier) 扩展，可以在编辑器里使用该规则格式化文件。

```json
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 50,
  "tabWidth": 2
}
```

### ESLint （MAIN)

[ESLint](https://github.com/eslint/eslint) 是一个查找 JavaScript / TypeScript 代码问题并提供修复建议的工具，换句话说就是可以约束的代码不会写出一堆 BUG ，它是代码健壮性的重要保障。

这里以一个 Vite + TypeScript + [Prettier](https://vue3.chengpeiquan.com/upgrade.html#prettier) 的 Vue 3 项目为例，在项目根目录下创建一个名为 `.eslintrc.js` 文件，写入以下内容：

```js
module.exports = {
  root: true,
  env: {
    node: true,
    browser: true,
  },
  extends: ['plugin:vue/vue3-essential', 'eslint:recommended', 'prettier'],
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser',
    ecmaVersion: 2020,
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint', 'prettier'],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'prettier/prettier': 'warn',
    'vue/multi-word-component-names': 'off',
  },
  globals: {
    defineProps: 'readonly',
    defineEmits: 'readonly',
    defineExpose: 'readonly',
    withDefaults: 'readonly',
  },
}
```

pnpm 安装对应的依赖 dependents：

```shell
pnpm install -D eslint eslint-config-prettier eslint-plugin-prettier eslint-plugin-vue @typescript-eslint/eslint-plugin @typescript-eslint/parser prettier
```

排除检查：`.eslintignore`

```
dist/*
```

## Auto import 自动导入

### Install Module

```shell
pnpm install -D unplugin-auto-import unplugin-vue-components
```

### Modify config file

`vite.config.ts`：

```js
import AutoImport from "unplugin-auto-import/vite";
import Components from "unplugin-vue-components/vite";

plugins: [
  AutoImport({
    // 自动导入 Vue 相关函数，如：ref, reactive, toRef 等
    imports: ["vue"],
    eslintrc: {
      enabled: true, // 是否自动生成 eslint 规则，建议生成之后设置 false 
      filepath: "./.eslintrc-auto-import.json", // 指定自动导入函数 eslint 规则的文件
    },
    dts: path.resolve(pathSrc, "types", "auto-imports.d.ts"), // 指定自动导入函数TS类型声明文件路径
  }),
  Components({
    dts: path.resolve(pathSrc, "types", "components.d.ts"), // 指定自动导入组件TS类型声明文件路径
  }),
]
```

`.eslintrc.cjs`

```json
"extends": [
    "./.eslintrc-auto-import.json"
],
```

`tsconfig.json`

```shell
{
  "include": ["src/**/*.d.ts"]
}
```

## SCSS

> 一款 CSS 预处理语言，SCSS 是 Sass 3 引入新的语法，其语法完全兼容 CSS3，并且继承了 Sass 的强大功能。

```shell
pnpm i -D sass
```

## Environment variate

项目根目录新建 `.env.development` 、`.env.production`

- 开发环境变量配置：.env.development

```js
# 变量必须以 VITE_ 为前缀才能暴露给外部读取 VITE_APP_TITLE = 'vue3-element-admin'
VITE_APP_PORT = 3000
VITE_APP_BASE_API = '/dev-api'
```

- 生产环境变量配置：.env.production

```properties
VITE_APP_TITLE = 'vue3-element-admin'
VITE_APP_PORT = 3000
VITE_APP_BASE_API = '/prod-api'
```

**环境变量智能提示**

新建 `src/types/env.d.ts` 文件存放环境变量 TS 类型声明。

```js
// src/types/env.d.ts
interface ImportMetaEnv {
  /**
   * 应用标题
   */
  VITE_APP_TITLE: string;
  /**
   * 应用端口
   */
  VITE_APP_PORT: number;
  /**
   * API基础路径(反向代理)
   */
  VITE_APP_BASE_API: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

## Vue3-router 路由

reference: [安装 | Vue Router](https://v3.router.vuejs.org/zh/installation.html)

install：

```shell
pnpm install vue-router
```

修改 main.ts 文件：

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
const app = createApp(App)

app.use(router).mount('#app')
```

### Import 导入

建立 src/router 目录，放 index.ts(入口文件) routes.ts (路由文件)。

src/router/index.ts

```js
// 路由导入

import { createRouter, createWebHistory } from 'vue-router'
import routes from './routes'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes,
})

// 路由守卫/钩子 hook
// 全局前置守卫 / 路由拦截
router.beforeEach((to, from) => {
  const { title } = to.meta
  document.title = (title as string) || '默认标题'

  // 判断登陆
  // const { isNoLogin } = to.meta
  // if (!isNoLogin) return '/login'
})

export default router

```

src/router/routes.ts

```js
// src/router/routes.ts 路由定义

import type { RouteRecordRaw } from 'vue-router'

// define routes
const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    name: 'home',
    component: () => import('@/App.vue'),
    meta: {
      title: 'main page',
    },
  },
  {
    path: '/:pathMatch(.*)*',
    name: '404',
    component: () => import('@/views/404.vue'),
  },
]

export default routes
```

## Pinia 全局状态管理

### Install

```shell
# 需要 cd 到的项目目录下
pnpm install pinia
```

src/main.ts

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { createPinia } from 'pinia'

createApp(App)
  .use(router)
  .use(createPinia())
  .mount('#app')

```

### Stores Folder

src/stores/index.ts 使用 `storeToRefs` 将 store 变为响应式，只需要导出文件的 config 即可。

```js
// src/stores/index.ts

import { defineStore } from 'pinia'
import { storeToRefs } from 'pinia'

export const userStore = defineStore('main', {
  state: () => {
    return {
      message: 'Hello World Pinia Store',
      randomMessages: <string[]>[],
    }
  },
})

export const useConfig = defineStore('config', {
  state: () => {
    return {
      debug: false,
      autoRefresh: false,
      autoRefreshInterval: 2000,
    }
  },
})

export const config = storeToRefs(useConfig())

```

## Element UI

### Install

```shell
pnpm install element-plus
```

按需要引入，安装在开发环境中：

```shell
pnpm install -D unplugin-vue-components unplugin-auto-import
```

修改 `vite.config.js`：

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'

// element UI
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import IconsResolver from "unplugin-icons/resolver";
import Icons from "unplugin-icons/vite";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      resolvers: [
      ElementPlusResolver(), // element func
      IconsResolver({}), // element Icon
      ],
	  vueTemplate: true, // 是否在 vue 模板中自动导入
	  dts: path.resolve(pathSrc, 'types', 'auto-imports.d.ts') // 自动导入组件类型声明文件位置，默认根目录
    }),
    Components({
      resolvers: [
      ElementPlusResolver()
	  IconsResolver({ enabledCollections: ["ep"] // element-plus图标库，其他图标库 https://icon-sets.iconify.design/ }),
	  })
      dts: path.resolve(pathSrc, "types", "components.d.ts"), // 自动导入组件类型声明文件位置，默认根目录
      ],
    }),
    // 自动安装图标库
	Icons({ autoInstall: true, }),
  ],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'), // 源码根目录
    },
  },
})
```

### Import

src/main.ts

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { createPinia } from 'pinia'
import 'element-plus/dist/index.css'

createApp(App)
  .use(router)
  .use(createPinia())
  .mount('#app')
```

## Axois 请求库

> Axios 基于 promise 可以用于浏览器和 node.js 的网络请求库

### Install

```
pnpm install axios
```

### Axios 工具类封装

`src/utils/request.js`  

初始化 axios instance 实例，包含异常处理等：

```js
import axios from 'axios'
import { AxiosResponse } from 'axios'
import { NoticeType, useNotice } from './notice'
import { config } from '@/stores'
import {
  InternalAxiosRequestConfig,
  AxiosError,
} from 'axios'

export const request = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 5000,
})

// 添加请求拦截器
request.interceptors.request.use(
  (axiosConfig: InternalAxiosRequestConfig) => {
    // 在发送请求之前做些什么
    if (config.debug.value) {
      useNotice(
        'Response',
        `url:${axiosConfig.url}`,
        NoticeType.INFO,
      )
    }

    return axiosConfig
  },
  (error) => {
    // 对请求错误做些什么
    return Promise.reject(error)
  },
)

// 添加响应拦截器
request.interceptors.response.use(
  (response: AxiosResponse) => {
    // 对响应数据做点什么
    // 成功响应数据
    if (config.debug.value) {
      useNotice(
        'Response',
        `url:${response.config.url}`,
        NoticeType.SUCCESS,
      )
    }
    return response
  },
  (error: AxiosError) => {
    console.log(error)
    if (error.response) {
      // 请求成功发出且服务器也响应了状态码，但状态代码超出了 2xx 的范围
      useNotice(
        `Response ERROR ${error.code}`,
        `${error.config?.url}\n${error.response.data}`,
        NoticeType.WARNING,
      )
    } else if (error.request) {
      // 请求已经成功发起，但没有收到响应
      // `error.request` 在浏览器中是 XMLHttpRequest 的实例，
      // 而在node.js中是 http.ClientRequest 的实例
      useNotice(
        `Response ERROR`,
        `${error.config?.url}\n${error}`,
        NoticeType.ERROR,
      )
    } else {
      useNotice(
        `Response ERROR`,
        `${error.config?.url}\nunknow http request error:${error}`,
        NoticeType.ERROR,
      )
    }
    return Promise.reject(error)
  },
)

```

高级封装：

```js
//  src/utils/request.ts
import axios, { InternalAxiosRequestConfig, AxiosResponse } from 'axios';
import { useUserStoreHook } from '@/store/modules/user';

// 创建 axios 实例
const service = axios.create({
  baseURL: import.meta.env.VITE_APP_BASE_API,
  timeout: 50000,
  headers: { 'Content-Type': 'application/json;charset=utf-8' }
});

// 请求拦截器
service.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    // 使用 Token
    const userStore = useUserStoreHook();
    if (userStore.token) {
      config.headers.Authorization = userStore.token;
    }
    return config;
  },
  (error: any) => {
    return Promise.reject(error);
  }
);

// 响应拦截器
service.interceptors.response.use(
  (response: AxiosResponse) => {
    const { code, msg } = response.data;
    // 登录成功
    if (code === '00000') {
      return response.data;
    }

    ElMessage.error(msg || '系统出错');
    return Promise.reject(new Error(msg || 'Error'));
  },
  (error: AxiosError) => {
    if (error.response.data) {
      const { code, msg } = error.response.data;
      // token 过期，跳转登录页
      if (code === 'A0230') {
        ElMessageBox.confirm('当前页面已失效，请重新登录', '提示', {
          confirmButtonText: '确定',
          type: 'warning'
        }).then(() => {
          localStorage.clear(); // @vueuse/core 自动导入
          window.location.href = '/';
        });
      }else{
          ElMessage.error(msg || '系统出错');
      }
    }
    return Promise.reject(error.message);
  }
);

// 导出 axios 实例
export default service;


```


src/plugins/api.ts 具体请求：

```js
// src/plugins/api.ts
import { request } from './useRequest'
import { ChatInfo } from '@/schema'

// get chat infomation /chat/all/

export async function getChats(): Promise<Array<ChatInfo> | void> {
  const resp = await request.get('/chat/all/')
  if (resp.data) {
    return resp.data as Array<ChatInfo>
  }
}
```

## Vueuse

[起步 | VueUse中文文档](https://www.vueusejs.com/guide/)

### Install

```shell
pnpm install @vueuse/core
```

## Reference

1. CSDN 博文：[Vue3.3 + Vite4.3 + TypeScript5+ Element-Plus：从零到一构建企业级后台管理系统（前后端开源）](https://blog.csdn.net/u013737132/article/details/130191394)

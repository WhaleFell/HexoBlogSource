---
title: Vue3 项目上手学习——V3 Admin
date: 2024-02-29 16:43:20
updated: 2024-02-29 16:43:20
categories:
tags:
description:
thumbnail:
banner_img:
---

# Vue3 项目上手学习——V3 Admin

## Start

学了那么久的 Vue3，终于到上手实战项目的时候了。之前一直是看视频教程，没有真正的上手打开 VSC 摸过项目，是时候写代码了。

这次选用的是 V3 Admin 这个项目，毕竟人家使用了主流技术 **Vue3 + Pinia + Router + Vite + Element-Plus** 。本文是在官网文档的基础上进一步抽丝剥茧分析这个项目，并提炼一些 **通用写法**。

reference：

1. GitHub [v3-admin-vite](https://github.com/un-pany/v3-admin-vite)
2. 项目对应老鸟中文文档：[V3 Admin Vite 中文文档 - 掘金](https://juejin.cn/post/7089377403717287972)
3. 适用于小白的 step by step tutorial [V3 Admin Vite - pany 的专栏 - 掘金](https://juejin.cn/column/7207659644487139387)

## Technology Stack 技术栈

-   **Vue3**：采用 Vue3 + script setup 最新的 Vue3 组合式 API
-   **Element Plus**：Element UI 的 Vue3 版本
-   **Pinia**: 传说中的 Vuex5
-   **Vite**：真的很快，取代 webpack
-   **Vue Router**：路由路由
-   **TypeScript**：JavaScript 语言的超集
-   **PNPM**：更快速的，节省磁盘空间的包管理工具
-   **Scss**：和 Element Plus 保持一致
-   **CSS 变量**：主要控制项目的布局和颜色
-   **ESlint**：代码校验
-   **Prettier**：代码格式化
-   **Axios**：发送网络请求（已封装好）
-   **UnoCSS**：具有高性能且极具灵活性的即时原子化 CSS 引擎
-   **注释**：各个配置项都写有尽可能详细的注释
-   **兼容移动端**: 布局兼容移动端页面分辨率

## Prepare 准备

### Install base Module

Proxy first，在 **墙国**，下个 npm 包前提是一个好用的代理：

```shell
npm config list
npm config set proxy=socks5://127.0.0.1:10808/
npm config delete proxy
```

Install module

```shell
npm install -g pnpm
# like npm insall xxx
pnpm add packageName

# initiate vite vue3
pnpm add vite -g
pnpm create vite@latest
pnpm install
pnpm add -D typescript ts-node @types/node less
pnpm add --save-dev --save-exact prettier
```

### Install ElementUI-Plus

```sh
pnpm install element-plus
```

In `main.ts`

```ts
// Element Plus
import ElementPlus from "element-plus"
import "element-plus/dist/index.css"

const app = createApp(App)

app.use(ElementPlus)
app.mount("#app")
```

### 安装按需引入

```sh
pnpm install -D unplugin-vue-components unplugin-auto-import
```

向 `vite.config.ts` 添加插件：

```ts
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

plugins: [
    // …
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
```

configure：

```ts
AutoImport({
      // 自动导入 Vue 相关函数，如：ref, reactive, toRef 等
      imports: ["vue"],
      // eslint auto import
      eslintrc: {
        enabled: true, // 是否自动生成 eslint 规则，建议生成之后设置 false
        filepath: "./.eslintrc-auto-import.json", // 指定自动导入函数 eslint 规则的文件
      },
      //
      resolvers: [ElementPlusResolver(), IconsResolver({})],
      vueTemplate: true, // 是否在 vue 模板中自动导入
      dts: path.resolve(pathSrc, "types", "auto-imports.d.ts"), // 指定自动导入函数TS类型声明文件路径
    }),
    // 组件
    Components({
      resolvers: [
        // 自动导入 Element Plus 组件
        ElementPlusResolver(),
        // 自动注册图标组件
        IconsResolver({
          enabledCollections: ["ep"],
        }),
      ],
      dts: path.resolve(pathSrc, "types", "components.d.ts"), // 指定自动导入组件TS类型声明文件路径
    }),
    // 图标
    Icons({
      // 自动安装图标库
      autoInstall: true,
    }),
```

## Configure 配置项

### Vscode code snippets

vue3 code snippet：

```json
{
    "Vue3 SFC template": {
        "prefix": "Vue3 SFC",
        "body": [
            "<template>",
            "\t<div class=\"app-container\">…</div>",
            "</template>\n",
            "<script lang=\"ts\" setup></script>\n",
            "<style scoped></style>",
            "$1"
        ],
        "description": "Vue3 SFC"
    }
}
```

vue3 hook code snippet:

```json
{
    "Vue3 Hook 代码结构一键生成": {
        "prefix": "Vue3 Hook",
        "body": [
            "import { ref } from \"vue\"\n",
            "const refName1 = ref<string>(\"This is a ref variable\")\n",
            "export function useHookName() {",
            "\tconst refName2 = ref<string>(\"This is a ref variable\")\n",
            "\tconst fnName = () => {}\n",
            "\treturn { refName1, refName2, fnName }",
            "}",
            "$1"
        ],
        "description": "Vue3 Hook"
    }
}
```

## Code specification 代码规范

### 命名规范

Components 组件和 Views 页面命名规范，这样做是为了更好的区分 component 和 view

1. **Components 组件**：都采用单词大写开头的  **大驼峰**  命名方式 (PascalCase)：`@/components/Index.vue` 、`@/components/NotifyList.vue`
2. **Views 页面**：页面由 components 组件组成，一律采用 **短横线连接 (kebab-case)**  的命名方式，比如：- `@/views/fetch-select.vue`

**hook 命名**：采用  **小驼峰 (camelCase)**，比如：`@/hooks/useTheme.ts`。

**Prop 命名**：在声明 prop 的时候，其命名应该始终采用  **小驼峰 (camelCase)**。

```ts
const props = defineProps({
    /** 打开全屏提示语 */
    openTips: {type: String, default: "全屏"},
    /** 关闭全屏提示语 */
    exitTips: {type: String, default: "退出全屏"},
})
```

### 代码规范

-   prop 的定义应该尽量详细，至少需要指定其类型
-   在组件上总是必须用 key 配合 v-for，以便维护内部组件及其子树的状态。
-   避免 v-if 和 v-for 用在一起。

### Annotation 注释规范

由于项目采用 TS 5.x 进行开发，为了获得更好的 TS 提示，项目采用了大量的  `/** xxx */`  注释，它的优点就是鼠标放上去会有注释显示出来。

## Code format and verify 代码格式化和校验

#### .tsconfig

**项目最重要的 ts 配置**

`.tsconfig`

```json
{
    "compilerOptions": {
        "target": "esnext",
        /** https://cn.vitejs.dev/guide/features.html#typescript-compiler-options */
        "useDefineForClassFields": true,
        "module": "esnext",
        "moduleResolution": "bundler",
        /** TS 严格模式 */
        "strict": true,
        "jsx": "preserve",
        "jsxImportSource": "vue",
        "importHelpers": true,
        "experimentalDecorators": true,
        "allowSyntheticDefaultImports": true,
        "sourceMap": true,
        "resolveJsonModule": true,
        /** https://cn.vitejs.dev/guide/features.html#typescript-compiler-options */
        "isolatedModules": true,
        "esModuleInterop": true,
        "lib": ["esnext", "dom"],
        "skipLibCheck": true,
        "types": [
            "node",
            "vite/client",
            /** Element Plus 的 Volar 插件支持 */
            "element-plus/global",
            "vitest"
        ],
        /** baseUrl 用来告诉编译器到哪里去查找模块，使用非相对模块时必须配置此项 */
        "baseUrl": ".",
        /** 非相对模块导入的路径映射配置，根据 baseUrl 配置进行路径计算 */
        "paths": {
            "@/*": ["src/*"]
        }
    },
    "include": [
        "src/**/*.ts",
        "src/**/*.d.ts",
        "src/**/*.tsx",
        "src/**/*.vue",
        "tests/**/*.ts",
        "types/**/*.d.ts",
        "vite.config.ts",
        "vitest.config.ts"
    ],
    /** 编译器默认排除的编译文件 */
    "exclude": ["node_modules", "dist"]
}
```

#### ESlint

-   `package.json` 文件的 `devDependencies` 中有所需的 ESlint 依赖包。
-   `ESlint` 的**配置文件**是根目录下的 `.eslintrc.cjs`，它里面定义了很多校验规则
-   `ESlint` 的**忽略文件**是根目录下的 `.eslintignore`，它里面定义的目录和文件都不会被 ESlint 检查

.eslintrc.cjs

```js
module.exports = {
    root: true,
    env: {
        browser: true,
        node: true,
        es6: true,
    },
    extends: [
        "plugin:vue/vue3-essential",
        "eslint:recommended",
        "@vue/typescript/recommended",
        "@vue/prettier",
        "@vue/eslint-config-typescript",
    ],
    parser: "vue-eslint-parser",
    parserOptions: {
        parser: "@typescript-eslint/parser",
        ecmaVersion: 2020,
        sourceType: "module",
        jsxPragma: "React",
        ecmaFeatures: {
            jsx: true,
            tsx: true,
        },
    },
    rules: {
        // TS
        "@typescript-eslint/no-explicit-any": "off",
        "no-debugger": "off",
        "@typescript-eslint/explicit-module-boundary-types": "off",
        "@typescript-eslint/ban-types": "off",
        "@typescript-eslint/ban-ts-comment": "off",
        "@typescript-eslint/no-empty-function": "off",
        "@typescript-eslint/no-non-null-assertion": "off",
        "@typescript-eslint/no-unused-vars": [
            "error",
            {
                argsIgnorePattern: "^_",
                varsIgnorePattern: "^_",
            },
        ],
        "no-unused-vars": [
            "error",
            {
                argsIgnorePattern: "^_",
                varsIgnorePattern: "^_",
            },
        ],
        // Vue
        "vue/no-v-html": "off",
        "vue/require-default-prop": "off",
        "vue/require-explicit-emits": "off",
        "vue/multi-word-component-names": "off",
        "vue/html-self-closing": [
            "error",
            {
                html: {
                    void: "always",
                    normal: "always",
                    component: "always",
                },
                svg: "always",
                math: "always",
            },
        ],
        // Prettier
        "prettier/prettier": [
            "error",
            {
                endOfLine: "auto",
            },
        ],
    },
}
```

.eslintignore：

```text
# Eslint 会忽略的文件

.DS_Store
node_modules
dist
dist-ssr
*.local
.npmrc
```

#### Prettier

-   `package.json` 文件的 `devDependencies` 中有所需的 Prettier 依赖包
-   `Prettier` 的**配置文件**是根目录下的 `prettier.config.js`，它里面定义了很多格式化规则
-   `Prettier` 的**忽略文件**是根目录下的 `.prettierignore`，它里面定义的目录和文件都不会被 Prettier 格式化

`prettier.config.js`：

```js
/**
 * 修改配置后重启编辑器
 * 配置项文档：https://prettier.io/docs/en/configuration.html
 * @type {import("prettier").Config}
 */

export default {
    /** 每一行的宽度 */
    printWidth: 120,
    /** 在对象中的括号之间是否用空格来间隔 */
    bracketSpacing: true,
    /** 箭头函数的参数无论有几个，都要括号包裹 */
    arrowParens: "always",
    /** 换行符的使用 */
    endOfLine: "auto",
    /** 是否采用单引号 */
    singleQuote: false,
    /** 对象或者数组的最后一个元素后面不要加逗号 */
    trailingComma: "none",
    /** 是否加分号 */
    semi: false,
    /** 缩进 */
    tabWidth: 2,
}
```

`.prettierignore`

```text
# Prettier 会忽略的文件

.DS_Store
node_modules
dist
dist-ssr
*.local
.npmrc
```

### vite.config.ts

项目 Vite 最重要的配置

`vite.config.ts`

```ts
/// <reference types="vitest" />

import { type ConfigEnv, type UserConfigExport, loadEnv } from "vite"
import path, { resolve } from "path"
import vue from "@vitejs/plugin-vue"
import { createSvgIconsPlugin } from "vite-plugin-svg-icons"
import svgLoader from "vite-svg-loader"
import UnoCSS from "unocss/vite"

/** 配置项文档：https://cn.vitejs.dev/config */
export default (configEnv: ConfigEnv): UserConfigExport => {
  // 从文件加载环境变量
  const viteEnv = loadEnv(configEnv.mode, process.cwd()) as ImportMetaEnv
  const { VITE_PUBLIC_PATH } = viteEnv
  return {
    /** 打包时根据实际情况修改 base */
    base: VITE_PUBLIC_PATH,
    resolve: {
      alias: {
        /** @ 符号指向 src 目录 */
        "@": resolve(__dirname, "./src")
      }
    },
    server: {
      /** 设置 host: true 才可以使用 Network 的形式，以 IP 访问项目 */
      host: true, // host: "0.0.0.0"
      /** 端口号 */
      port: 3333,
      /** 是否自动打开浏览器 */
      open: false,
      /** 跨域设置允许 */
      cors: true,
      /** 端口被占用时，是否直接退出 */
      strictPort: false,
      /** 接口代理 */
      proxy: {
        "/api/v1": {
          target: "https://mock.mengxuegu.com/mock/63218b5fb4c53348ed2bc212",
          ws: true,
          /** 是否允许跨域 */
          changeOrigin: true
        }
      },
      /** 预热常用文件，提高初始页面加载速度 */
      warmup: {
        clientFiles: []
      }
    },
    build: {
      /** 单个 chunk 文件的大小超过 2048KB 时发出警告 */
      chunkSizeWarningLimit: 2048,
      /** 禁用 gzip 压缩大小报告 */
      reportCompressedSize: false,
      /** 打包后静态资源目录 */
      assetsDir: "static",
      rollupOptions: {
        output: {
          /**
           * 分块策略
           * 1. 注意这些包名必须存在，否则打包会报错
           * 2. 如果你不想自定义 chunk 分割策略，可以直接移除这段配置
           */
          manualChunks: {
            vue: ["vue", "vue-router", "pinia"],
            element: ["element-plus", "@element-plus/icons-vue"],
            // vxe: ["vxe-table", "vxe-table-plugin-element", "xe-utils"]
          }
        }
      }
    },
    /** 混淆器 */
    esbuild: {
      /** 打包时移除 console.log */
      pure: ["console.log"],
      /** 打包时移除 debugger */
      drop: ["debugger"],
      /** 打包时移除所有注释 */
      legalComments: "none"
    },
    /** Vite 插件 */
    plugins: [
      vue(),
      // vueJsx(),
      /** 将 SVG 静态图转化为 Vue 组件 */
      svgLoader({ defaultImport: "url" }),
      /** SVG */
      // createSvgIconsPlugin({
        iconDirs: [path.resolve(process.cwd(), "src/icons/svg")],
        symbolId: "icon-[dir]-[name]"
      }),
      /** UnoCSS */
      UnoCSS()
    ],
    /** Vitest 单元测试配置：https://cn.vitest.dev/config */
    test: {
      include: ["tests/**/*.test.ts"],
      environment: "jsdom"
    }
  }
}

```

### .env

环境变量

`.env`

```text
# 所有环境自定义的环境变量（命名必须以 VITE_ 开头）

## 项目标题
VITE_APP_TITLE = V3 Admin Vite

##  打包路径（就是网站前缀，例如部署到 https://whaleluo.top/v3-admin-vite/ 域名下，就需要填写 /v3-admin-vite/）
VITE_PUBLIC_PATH = '/'
```

### .editorConfig

**editorConfig** 是一个适用于 **多语言**，多 IDE 的代码格式化工具。有利于各种项目 **形成统一的代码格式风格**。  
建议在所有项目中启动 **editorConfig** 因为真的很好用。

.editorConfig

```ini
# 修改配置后重启编辑器
# 配置项文档：https://editorconfig.org/

# 告知 EditorConfig 插件，当前即是根文件
root = true

# python format config
[*.py]
indent_style = space
indent_size = 4

# 适用全部文件
[*]
## 设置字符集
charset = utf-8
## 缩进风格 space | tab，建议 space（会自动继承给 Prettier）
indent_style = space
## 缩进的空格数（会自动继承给 Prettier）
indent_size = 2
## 换行符类型 lf | cr | crlf，一般都是设置为 lf
end_of_line = lf
## 是否在文件末尾插入空白行
insert_final_newline = true
## 是否删除一行中的前后空格
trim_trailing_whitespace = true

# 适用 .md 文件
[*.md]
insert_final_newline = false
trim_trailing_whitespace = false
```

#### .npmrc

npm、cnpm 包管理工具的配置项，设置代理之类的。

`.npmrc`

```ini
# 通过该配置兜底解决组件没有类型提示的问题
shamefully-hoist = true
# Proxy
# registry = https://registry.npm.taobao.org
https-proxy = socks5://127.0.0.1:10808
http-proxy = socks5://127.0.0.1:10808
```

#### Vscode

settings.json

```json
{
    "editor.codeActionsOnSave": {"source.fixAll.eslint": "explicit"},
    "[vue]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[javascript]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[typescript]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[json]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[jsonc]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[html]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[css]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
    "[scss]": {"editor.defaultFormatter": "esbenp.prettier-vscode"}
}
```

### Git commit 规范

-   **feat:** 增加新的业务功能
-   **fix:** 修复业务问题/BUG
-   **perf:** 优化性能
-   **style:** 更改代码风格, 不影响运行结果
-   **refactor:** 重构代码
-   **revert:** 撤销更改
-   **test:** 测试相关, 不涉及业务代码的更改
-   **docs:** 文档和注释相关
-   **chore:** 更新依赖/修改脚手架配置等琐事
-   **workflow:** 工作流改进
-   **ci:** 持续集成相关
-   **types:** 类型定义文件更改
-   **wip:** 开发中

**完成一件事情，就提交一次 commit**。而不是等到你写完一整天的代码后，才在下班前只提交一次。

## vue-router 路由

CH Document：[安装 | Vue Router](https://router.vuejs.org/zh/installation.html)

### Install

```shell
pnpm add vue-router@4
```

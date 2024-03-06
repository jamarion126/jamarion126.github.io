---
title: uniapp项目使用tailwindcss
date: 2023-12-12 16:41:26
excerpt: ice breaker 大佬开发的插件 一条路直接安装
toc: true
tags:
  - vue
  - vue3
  - uniapp
  - tailwindcss
---

官网地址：[weapp-tailwindcss](https://weapp-tw.icebreaker.top/)

### 使用包管理器安装 tailwindcss

```shell shell
yarn add -D tailwindcss postcss autoprefixer
```

### 初始化 tailwind.config.js 文件

```shell shell
npx tailwindcss init
```

### 在项目目录下创建 postcss.config.js 并注册 tailwindcss

```javascript postcss.config.js
// postcss.config.js
// 假如你使用的框架/工具不支持 postcss.config.js 配置文件，则可以使用内联的写法
module.exports = {
  plugins: {
    tailwindcss: {},
    // 假如框架已经内置了 `autoprefixer`，可以去除下一行
    autoprefixer: {},
  },
};
```

### 配置 tailwind.config.js

```javascript tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  // 这里给出了一份 uni-app /taro 通用示例，具体要根据你自己项目的目录结构进行配置
  // 不在 content 包括的文件内，你编写的 class，是不会生成对应的css工具类的
  content: ["./public/index.html", "./src/**/*.{html,js,ts,jsx,tsx,vue}"],
  // 其他配置项
  // ...
  corePlugins: {
    // 小程序不需要 preflight，因为这主要是给 h5 的，如果你要同时开发小程序和 h5 端，你应该使用环境变量来控制它
    preflight: false,
  },
};
```

### 引入 tailwindcss

```css App.vue
<style>
@tailwind base;
@tailwind components;
@tailwind utilities;
/* 使用 scss */
/* @import 'tailwindcss/base'; */
/* @import 'tailwindcss/utilities'; */
/* @import 'tailwindcss/components'; */
</style>
```

### 安装 weapp-tailwindcss

```shell shell
yarn add -D weapp-tailwindcss
```

并运行 `npx weapp-tw patch`

然后把下列脚本，添加进你的 package.json 的 scripts 字段里:

```shell package.json
"postinstall": "weapp-tw patch"
```

### 创建 platform.js 在根目录

```javascript platform.js
const isH5 = process.env.UNI_PLATFORM === "h5";
const isApp = process.env.UNI_PLATFORM === "app";
const isMpWeiXin = process.env.UNI_PLATFORM === "mp-weixin";

module.exports = {
  isH5,
  isApp,
  isMpWeiXin,
};
```

### 修改 vite.config

```ts vite.config.ts
// vite.config.[jt]s
import { defineConfig } from "vite";
import uni from "@dcloudio/vite-plugin-uni";
import { UnifiedViteWeappTailwindcssPlugin as uvwt } from "weapp-tailwindcss/vite";

export default defineConfig({
  // uni 是 uni-app 官方插件， uvtw 一定要放在 uni 后，对生成文件进行处理
  plugins: [uni(), uvwt()],
  css: {
    postcss: {
      plugins: [
        // require('tailwindcss')() 和 require('tailwindcss') 等价的，表示什么参数都不传，如果你想传入参数
        // require('tailwindcss')({} <- 这个是postcss插件参数)
        require("tailwindcss"),
        require("autoprefixer"),
      ],
    },
  },
});
```

### 插件内置 rem 转 rpx 功能

在 `^3.0.0` 版本中，所有插件都内置了 `rem2rpx` 参数，默认不开启，要启用它只需将它设置成 `true` 即可

```ts vite.config.ts
import { UnifiedViteWeappTailwindcssPlugin } from "weapp-tailwindcss/vite";

UnifiedViteWeappTailwindcssPlugin({
  rem2rpx: true,
});
```

`App` 平台编译会报错，这时候要使用根据条件不编译到 `App`

```ts vite.config.ts
import { UnifiedViteWeappTailwindcssPlugin } from "weapp-tailwindcss/vite";
import { isH5, isApp, isMpWeiXin } from "./platform";

UnifiedViteWeappTailwindcssPlugin({
  rem2rpx: true,
  disabled: isApp,
});
```

### 使用 Prettier 自动进行类别排序

github 地址：[prettier-plugin-tailwindcss](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)

```shell shell
yarn add -D prettier prettier-plugin-tailwindcss
```

根目录新增 `prettier.config.js`

```javascript prettier.config.js
// prettier.config.js
module.exports = {
  plugins: ["prettier-plugin-tailwindcss"],
};
```

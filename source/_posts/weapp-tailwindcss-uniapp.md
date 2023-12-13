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

## weapp-tailwindcss

官网地址：[weapp-tailwindcss](https://weapp-tw.icebreaker.top/)

### 使用包管理器安装 tailwindcss

```shell shell
yarn add -D tailwindcss postcss autoprefixer
```

### 初始化 tailwind.config.js 文件

```shell shell
npx tailwindcss init
```

### 创建 postcss.config.cjs

```javascript  postcss.config.cjs
/* eslint-disable @typescript-eslint/no-var-requires */
// const { WeappTailwindcssDisabled } = require('./platform')

/**
 * @type {import('postcss').AcceptedPlugin[]}
 */
const plugins = [require('tailwindcss')(), require('autoprefixer')()]

// 下方为 px 转 rpx 功能
// if (!WeappTailwindcssDisabled) {
// plugins.push(
//   require('postcss-pxtransform')({
//     platform: 'weapp',
//     // https://taro-docs.jd.com/docs/size
//     // 根据你的设计稿宽度进行配置
//     // 可以传入一个 function
//     // designWidth (input) {
//     //   if (input.file.replace(/\\+/g, '/').indexOf('@nutui/nutui-taro') > -1) {
//     //     return 375
//     //   }
//     //   return 750
//     // },
//     designWidth: 750, // 375,
//     deviceRatio: {
//       640: 2.34 / 2,
//       // 此时应用到的规则，代表 1px = 1rpx
//       750: 1,
//       828: 1.81 / 2,
//       // 假如你把 designWidth 设置成 375 则使用这条规则 1px = 2rpx
//       375: 2 / 1
//     }
//   })
// )
// }
plugins.push(require('weapp-tailwindcss/css-macro/postcss'))

module.exports = plugins
```

### 配置 tailwind.config.js

```javascript tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  // 这里给出了一份 uni-app /taro 通用示例，具体要根据你自己项目的目录结构进行配置
  // 不在 content 包括的文件内编写的 class，不会生成对应的工具类
  content: ['./public/index.html', './src/**/*.{html,js,ts,jsx,tsx,vue}'],
  // 其他配置项
  // ...
  corePlugins: {
    // 不需要 preflight，因为这主要是给 h5 的，如果你要同时开发小程序和 h5 端，你应该使用环境变量来控制它
    preflight: false
  }
}
```

### 引入 tailwindcss

```css App.vue
@tailwind base;
@tailwind components;
@tailwind utilities;
/* 使用 scss */
/* @import 'tailwindcss/base'; */
/* @import 'tailwindcss/utilities'; */
/* @import 'tailwindcss/components'; */
```

### 外置 postcss 插件

```shell shell
yarn add -D postcss-rem-to-responsive-pixel
```

### 安装weapp-tailwindcss

```shell shell
yarn add -D weapp-tailwindcss
```

并运行 `npx weapp-tw patch`

然后把下列脚本，添加进你的 package.json 的 scripts 字段里:

``` shell package.json
"postinstall": "weapp-tw patch"
```

### 创建platform.js在根目录

```javascript platform.js
const isH5 = process.env.UNI_PLATFORM === 'h5'
const isApp = process.env.UNI_PLATFORM === 'app'
const WeappTailwindcssDisabled = isH5 || isApp

module.exports = {
    isH5,
    isApp,
    WeappTailwindcssDisabled
}
```

### 修改vite.config

```ts vite.config.ts
// vite.config.[jt]s
import {defineConfig} from "vite";
import uni from "@dcloudio/vite-plugin-uni";
import {UnifiedViteWeappTailwindcssPlugin as uvwt} from 'weapp-tailwindcss/vite';
import {WeappTailwindcssDisabled} from './platform'
import postcssPlugins from './postcss.config.cjs'
// uni 是 uni-app 官方插件， uvtw 一定要放在 uni 后，对生成文件进行处理
const vitePlugins = [
    uni(),
    uvwt({
        rem2rpx: true,
        disabled: WeappTailwindcssDisabled
    })
]

export default defineConfig({
    plugins: vitePlugins,
// 内联 postcss 注册 tailwindcss
    css: {
        postcss: {
            plugins: postcssPlugins
        }
    }
});

```

### 使用 Prettier 自动进行类别排序

github地址：[prettier-plugin-tailwindcss](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)

```shell shell
yarn add -D prettier prettier-plugin-tailwindcss
```

根目录新增 `prettier.config.js`

```javascript prettier.config.js
// prettier.config.js
module.exports = {
  plugins: ['prettier-plugin-tailwindcss'],
}
```
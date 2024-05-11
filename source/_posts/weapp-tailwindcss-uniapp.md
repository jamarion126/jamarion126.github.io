---
title: uniapp项目使用tailwindcss
date: 2023-12-12 16:41:26
excerpt: ice breaker 大佬开发的插件 一条路直接安装
toc: true
tags:
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

### 配置 tailwind.config.js

```javascript tailwind.config.js
import { isH5, isApp, isMpWeiXin } from "./platform";
module.exports = {
  content: ["./public/index.html", "./src/**/*.{html,js,ts,jsx,tsx,vue}"],
  corePlugins: {
    // 小程序不需要 preflight，因为这主要是给 h5 的，如果你要同时开发小程序和 h5 端，你应该使用环境变量来控制它
    preflight: isH5,
  },
};
```

### 引入 tailwindcss

```css App.vue
@tailwind base;
@tailwind components;
@tailwind utilities;
```
或者
```scss
@import 'tailwindcss/base';
@import 'tailwindcss/utilities';
@import 'tailwindcss/components';
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

### 修改 vite.config

```ts vite.config.ts
// vite.config.[jt]s
import { defineConfig } from "vite";
import uni from "@dcloudio/vite-plugin-uni";
import { UnifiedViteWeappTailwindcssPlugin } from "weapp-tailwindcss/vite";
import { isH5, isApp, isMpWeiXin } from "./platform";

export default defineConfig({
  // uni 是 uni-app 官方插件， uvtw 一定要放在 uni 后，对生成文件进行处理
  plugins: [uni(), uvwt({
      rem2rpx: true,
      disabled: isApp || isH5,
  })],
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
    printWidth: 140, //单行长度
    tabWidth: 2, //缩进长度
    useTabs: false, //使用空格代替tab缩进
    semi: false, //句末使用分号
    singleQuote: false, //使用单引号
    quoteProps: "as-needed", //仅在必需时为对象的key添加引号
    jsxSingleQuote: true, // jsx中使用单引号
    trailingComma: "all", //多行时尽可能打印尾随逗号
    bracketSpacing: true, //在对象前后添加空格-eg: { foo: bar }
    jsxBracketSameLine: true, //多属性html标签的‘>’折行放置
    arrowParens: "always", //单参数箭头函数参数周围使用圆括号-eg: (x) => x
    requirePragma: false, //无需顶部注释即可格式化
    insertPragma: false, //在已被preitter格式化的文件顶部加上标注
    proseWrap: "preserve", //不知道怎么翻译
    htmlWhitespaceSensitivity: "ignore", //对HTML全局空白不敏感
    vueIndentScriptAndStyle: false, //不对vue中的script及style标签缩进
    endOfLine: "lf", //结束行形式
    embeddedLanguageFormatting: "auto", //对引用代码进行格式化
}

```

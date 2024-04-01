---
title: 表单校验
excerpt: async-validator 使用
cover: https://www.toopic.cn/public/uploads/small/1710846337184171084633786.jpg
date: 2024-04-01 17:01:38
tags:
  - form
  - async-validator
---

github地址：[async-validator](https://github.com/yiminghe/async-validator)

### 安装 async-validator

```shell shell
yarn add async-validator
```

### 新建文件

```ts ts
import Schema, { type Rules } from "async-validator"

export async function useValidator(form: any, rules: Rules, key?: string) {
    let _rules: any = {}

    if (key) _rules[key] = rules[key]
    else _rules = rules

    const validator: Schema = new Schema(_rules)

    return new Promise((resolve, reject) => {
        validator
            .validate(form, { first: true })
            .then(resolve)
            .catch((error) => reject(error.errors[0]))
    })
}

```

### 使用

```vue vue
<template>
  <view class="space-y-4 p-4">
    <view class="space-y-2">
      <view class="rounded-sm bg-red-200 px-4 py-2">
        <input placeholder="请输入手机号" v-model="form.phone" @blur="handleBlur('phone')" />
      </view>
      <view class="text-xs text-red-500">{{ messages.phone }}</view>
    </view>
    <view class="space-y-2">
      <view class="rounded-sm bg-red-200 px-4 py-2">
        <input placeholder="请输入邮箱" v-model="form.email" @blur="handleBlur('email')" />
      </view>
      <view class="text-xs text-red-500">{{ messages.email }}</view>
    </view>
    <view class="space-y-2">
      <view class="rounded-sm bg-red-200 px-4 py-2">
        <input placeholder="请输入密码" v-model="form.password" @blur="handleBlur('password')" />
      </view>
      <view class="text-xs text-red-500">{{ messages.password }}</view>
    </view>
    <view>
      <wd-button block @click="onSubmit">提交</wd-button>
    </view>
  </view>
</template>

<script setup lang="ts">
import regular from "@/utils/regular"
import { useValidator } from "@/hooks/validator"

const rules = {
  phone: [
    { required: true, message: "请输入手机号" },
    { len: 11, message: "请输入11位手机号" },
    { pattern: regular.phone, message: "手机号格式错误" },
  ],
  email: [
    { required: true, message: "请输入邮箱" },
    { pattern: regular.email, message: "邮箱格式错误" },
  ],
  password: [
    { required: true, message: "请输入密码" },
    { min: 8, message: "请输入最少八位密码" },
    { pattern: regular.password, message: "密码只能由数字英文以及下划线组成" },
  ],
}

const form = reactive({
  phone: "13574757122",
  email: "min126@qq.com",
  password: "",
})

const messages: any = reactive({
  phone: "",
  email: "",
  password: "",
})

const handleBlur = (key: string) => {
  useValidator(form, rules, key)
    .then(() => {
      messages[key] = ""
    })
    .catch((error) => {
      messages[key] = error.message
    })
}

const onSubmit = () => {
  useValidator(form, rules)
    .then((fields) => {
      console.log(fields)
    })
    .catch((error) => {
      console.log(error)
    })
}
</script>

<style scoped lang="scss"></style>

```
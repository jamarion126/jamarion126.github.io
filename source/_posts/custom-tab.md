---
title: 自定义 Tabs 组件
excerpt: 用uniapp自定义顺滑下划线Tabs
date: 2023-12-07 09:55:09
toc: true
tags:
- uniapp
- 组件
- vue
---

## template

``` javascript template
<template>
  <view class="tabs">
    <view class="tabs-list">
      <view
        v-for="(item, index) in data"
        :key="index"
        :class="[index === value ? 'tabs-list-item-select' : 'tabs-list-item']"
        @click="onChangeTab(index)"
      >
        {{ item }}
      </view>
    </view>
    <view class="line" :style="inlineStyle" />
  </view>
</template>
```

- `tabs` 是最外层的盒子
- `tabs-list` 是标签
- `line` 是下面的下划线
- `tabs-list-item-select` 是被选中的那一个

## script

``` javascript script
<script setup lang="ts">
import { nextTick, onMounted, reactive, ref } from "vue";

interface Props {
  data: Array<string>;
  value: number;
}

const props = withDefaults(defineProps<Props>(), {
  value: 0,
});

const $emits = defineEmits(["update:value"]);

// 整个tab的dom数据
const tabDom = ref<DOMRect>({});

// 下划线需要增加的样式
const inlineStyle = reactive({
  transition: "all 0.3s ease-in-out",
  left: "",
  width: "",
});

// 刚进来就需要滑动到指定的位置
onMounted(() => {
  onChangeTab(props.value);
  getDom(".tabs", (rect: DOMRect) => {
    tabDom.value = rect;
  });
});

// 点击滑动到指定位置
const onChangeTab = (index: number) => {
  $emits("update:value", index);
  nextTick().then(() => {
    getDom(".tabs-list-item-select", (rect: DOMRect) => {
      inlineStyle.left = rect.left - tabDom.value.left + rect.width / 2 + "px";
      inlineStyle.width = rect.width / 2 + "px";
    });
  });
};

// 获取dom数据
const getDom = (domName: string, callback: Function) => {
  uni
    .createSelectorQuery()
    .select(domName)
    .boundingClientRect((rect) => {
      callback(rect);
    })
    .exec();
};
</script>
```

说说我的逻辑
- 点击到哪个，我们就需要拿到被点击 `dom` 的位置信息，这里是 `getDom` 方法去拿
- 拿到数据后，我们会看到有个 `left`，这个数据就是代表他离左侧的距离
- 因为我们的下划线是 `定位的`，我们需要把这个 `left` 去修改下划线的位置，这样就行了

{% message color:info icon:"fa-solid fa-audio-description" title:提示 %}
- **问**：为什么要 `rect.left - tabDom.value.left`，为什么不直接 `rect.left`

- **答**：因为我们的下划线是针对 `tabs` 定位的，直接 `rect.left` 是针对于整个视图的，所以我们需要减去 `tabDom.value.left`
{% endmessage %}

## css

``` scss css
<style scoped lang="scss">
$theme-color: #e43d30;

.tabs {
  position: relative;
  height: 2rem;

  .tabs-list {
    display: flex;
    gap: 1rem;
  }

  .tabs-list-item,
  .tabs-list-item-select {
    transition: all 0.2s;
    font-size: 0.85rem;
  }

  .tabs-list-item-select {
    scale: 1.05;
    color: $theme-color;
    font-weight: 700;
  }

  .line {
    position: absolute;
    bottom: 0;
    height: 0.15rem;
    transform: translateX(-50%);
    border-radius: 0.5rem;
    background: $theme-color;
  }
}
</style>
```
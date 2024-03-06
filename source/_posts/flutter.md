---
title: flutter
excerpt: flutter学习，必学成！！
cover: https://www.toopic.cn/public/uploads/small/1709102748691170910274859.jpg
date: 2024-03-06 11:24:24
tags: flutter
---

### 导航返回拦截

```dart
Widget PopScope({
    Key? key,
    required Widget child,
    bool canPop = true,
    void Function( bool didPop )? onPopInvoked,
})
```

`canPop` 当为 `false` 时，阻止当前路由被弹出。

`onPopInvoked` 需要判断 `didPop` 是否返回

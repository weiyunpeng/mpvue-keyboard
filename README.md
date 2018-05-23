# mpvue-keyboard 基于 mpvue的小程序虚拟键盘组件

[![npm package](https://img.shields.io/npm/v/mpvue-keyboard.svg)](https://www.npmjs.com/package/mpvue-keyboard)
[![npm downloads](http://img.shields.io/npm/dm/mpvue-keyboard.svg)](https://www.npmjs.com/package/mpvue-keyboard)

## 效果图
![默认效果1](./static/1.png)
![默认效果2](./static/2.png)

## 属性

| 名称              | 类型           | 默认值        | 描述               |
| -----------------|--------------- | ------------- | ----------------  |
| title        | String         | 输入或拍照录入车牌             | 自定义 标题  |
| bgcolor          | String         | #ffffff             | 自定义背景颜色           |
| baseBorColor           | String         | #cccccc   | 自定义输入格子边框颜色  |
| activeBorColor     | String       | #ff7149          | 自定义正在输入的格子边框颜色 |
| keyboard       | Function       | null          | 获取键盘输入结果 |

## 基本使用方法

* 安装

``` bash
npm i mpvue-keyboard --save
```

* 使用

``` vue
<template>
<view>车牌号为：<text style="font-size:60rpx">{{msg}}</text></view>
    <keyboard :title="'输入车牌号'" @keyboard="change"></keyboard>
</template>

<script>
import Keyboard from 'mpvue-keyboard';
export default {
    components: {
        keyboard: Keyboard
    },
    data() {
        return {
            msg:msg
        };
    },
    computed: {},
    methods: {
        change(msg) {
            console.log(msg);
            this.msg = msg
        }
    },
    mounted() {}
};
</script>

```
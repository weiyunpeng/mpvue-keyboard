# mpvue-keyboard 基于 mpvue的小程序虚拟键盘组件

[![npm package](https://img.shields.io/npm/v/mpvue-keyboard.svg)](https://www.npmjs.com/package/mpvue-keyboard)
[![npm downloads](http://img.shields.io/npm/dm/mpvue-keyboard.svg)](https://www.npmjs.com/package/mpvue-keyboard)

## 效果图
![默认效果1](./static/1.jpg)
![默认效果2](./static/2.jpg)

## API

| 名称              | 类型           | 默认值        | 描述               |
| -----------------|--------------- | ------------- | ----------------  |
| show        | Boolean         |     -         | 是否显示键盘  |
| title        | String         | -             | 自定义 标题  |
| base-border           | String         | #cccccc   | 自定义输入格子边框颜色  |
| active-border     | String       | #ff7149          | 自定义正在输入的格子边框颜色 |
| extra-key     | String       | 完成          | 右下角按键内容 |
| keyboard       | Function       | null          | 获取键盘输入结果 |


## Event

| 事件名              | 说明           | 参数        |
| -----------------|--------------- | ----------------  |
| keyboard        | 点击完成键时触发         |     key: 按键所选择的内容         |
| input        | 点击按键时触发         |     -         |
| close        | 点击关闭按钮或非键盘区域时触发         |     -         |
| delete        | 点击删除键时触发         |     -         |

## 基本使用方法

* 安装

``` bash
npm i mpvue-keyboard --save
```

* 使用

``` vue
<template>
<view>车牌号为：<text style="font-size:60rpx">{{msg}}</text></view>
     <keyboard :show="true" extra-key="支付" @keyboard="keyboardChange" active-border="#0deafe" base-border="38f8f8f" @input="inputChange" @close="closeChange" @delete="delChnage"></keyboard>
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
            this.msg = msg
        },
        inputChange() {
            console.log('get input');
        },
        closeChange() {
            console.log('get close');
        },
        delChnage() {
            console.log('get del')
        }
    },
    mounted() {}
};
</script>


```
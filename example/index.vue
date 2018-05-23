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

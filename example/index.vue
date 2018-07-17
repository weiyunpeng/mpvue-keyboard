<template>
<view>车牌号为：<text style="font-size:60rpx">{{msg}}</text></view>
     <keyboard @keyboard="change" activeBorColor="#0deafe" baseBorColor="38f8f8f"></keyboard>
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
        }
    },
    mounted() {}
};
</script>

<template>
    <view>车牌号为：<text style="font-size:60rpx">{{msg}}</text></view>
    <keyboard :show="true" extra-key="支付" @keyboard="keyboard" active-border="#0deafe" base-border="38f8f8f" @input="inputChange" @close="closeChange" @delete="delChnage"></keyboard>
</template>

<script>
import Keyboard from 'mpvue-keyboard';
export default {
    components: {
        keyboard: Keyboard
    },
    data() {
        return {
            msg: ''
        };
    },
    computed: {},
    methods: {
        keyboard(msg) {
            this.msg = msg;
        },
        inputChange() {
            console.log('get input');
        },
        closeChange() {
            console.log('get close');
        },
        delChnage() {
            console.log('get del');
        }
    },
    mounted() {}
};
</script>
